# Byline — Technical Specification

**Version:** 0.1-draft
**Last updated:** 2026-04-13
**Status:** Draft — awaiting name confirmation and NQ7 MVP scope decision

---

## 1. Overview

**Project name:** TBD (working title: Byline)
**Type:** Long-form blogging platform on Nostr
**Core function:** Writers publish markdown stories to the Nostr network; readers discover and read them via a web interface with Lightning Network zaps.

**One-liner:** Long-form stories on Nostr, with Lightning-powered appreciation.

---

## 2. Protocol Foundation

Byline is built entirely on open Nostr NIPs. We follow, not fork.

| NIP | Purpose |
|-----|---------|
| NIP-01 | Base protocol (events, keys, relay communication) |
| NIP-19 | naddr / npub / nevent encoding |
| NIP-23 | **Kind 30023** — long-form articles (primary storage format) |
| NIP-24 | Long-form content metadata (extra tags) |
| NIP-57 | **Kind 9734/9735** — Lightning zaps |
| NIP-07 | Browser extension signing (Alby, nos2x) |
| NIP-46 | Delegated signing (deferred, v2+) |
| NIP-51 | Mute/block lists, bookmark lists |

---

## 3. Content Storage

### Article Event (NIP-23, kind 30023)

```json
{
  "kind": 30023,
  "content": "# Markdown content...",
  "tags": [
    ["d", "article-slug"],
    ["title", "Article Title"],
    ["published_at", "1699999999"],
    ["t", "byline"],
    ["t", "byline/username"],
    ["t", "topic"],
    ["image", "https://..."],
    ["summary", "..."]
  ],
  "pubkey": "<author hexpubkey>",
  "created_at": 1700000000
}
```

**Rules:**
- `d` tag is the article's unique identifier — used for editing and referencing
- Editing publishes a **new event** with updated `created_at` — Byline shows only the latest version per `d` tag
- `published_at` is set on first publish and never changes
- Content is plain Markdown — no HTML
- Articles under ~50KB content; images hosted externally

### Drafts (kind 30024)
- Same structure as kind 30023 but not shown on public feed
- Byline auto-saves drafts locally and optionally publishes to kind 30024

### Replies (kind 1111, NIP-22)
- Comments on articles use kind 1111
- Threaded under the parent article's `d` tag

### Deletion
- Nostr supports deletion via kind 5 events (NIP-23 references this)
- Byline respects deletion: if an article's author publishes a kind 5 delete event, Byline removes it from the featured page

---

## 4. Discovery & Tagging

### Discovery Tag
**TBD** — the tag that marks all Byline content for discovery.
- Working default: `byline`
- Authors MUST include this tag on all published articles
- Filtered pages query for kind 30023 + discovery tag

### Sub-tags (per-author, per-topic)
- `byline/<username>` — author's blog stream
- `byline/<topic>` — topic streams (e.g., `byline/cooking`)
- Standard Nostr `t` tags for cross-topic discovery

### Front Page
- Shows all kind 30023 articles with the discovery tag, sorted by recency + engagement
- **Featured page:** curated subset — TBD curation criteria (NQ4)
- AI moderation for featured page inclusion (NQ4)

### Filter Page
- Browse by tag
- Search by npub or topic
- Filter by author

### SEO
- Next.js SSR renders articles with full meta tags
- Google can crawl public article pages
- `naddr` codes provide canonical article URLs

---

## 5. Lightning / Zaps (NIP-57)

### Author Setup
- Author needs a **Lightning Address** in their Nostr kind-0 profile (`lud16` field)
- Any Lightning Address provider works (Alby, Voltage, etc.)
- No Byline-specific Lightning node required

### Reader Zaps
1. Reader clicks "Zap" on an article
2. Byline calls `window.webln.enable()` (Alby browser extension)
3. Alby handles invoice generation via the author's lnurl server
4. Alby pays the invoice
5. Author's lnurl server publishes kind 9735 (zap receipt) to relays
6. Byline watches for 9735 events and displays zap count

### Zap Split (Platform Cut)
- **Confirmed:** Yes, small Byline platform cut
- Implementation: NIP-57 multi-p tag with weights
- Default split: 95% author / 5% Byline
- **TBD:** Whether this is opt-in or default, how Byline's Lightning Address is configured

### Payment Flow (No Full Node Needed)
```
Byline → window.webln (Alby) → author's lnurl server → Lightning Network
                                        ↓
                            zap receipt (kind 9735) published to relays
                                        ↓
                            Byline displays zap count
```

---

## 6. Authentication & Identity

### Login Flow (Primary: Alby)
1. User clicks "Connect with Alby"
2. Byline calls `window.alby.enable()` — Alby popup opens
3. Alby returns: `{ nostrPubkey, walletBalance, lnurl }`
4. Byline uses `window.nostr.signEvent()` when publishing articles
5. Private key never touches Byline servers

### Login Flow (Fallback)
- **nos2x extension:** NIP-07 signing without Lightning
- **In-browser keygen:** nostr-tools generates keypair in browser, stored in localStorage with backup warning
- **NWC:** For users without Alby, Nostr Wallet Connect links any Lightning wallet

### Profile
- Name, picture, bio: fetched from Nostr kind-0 profile on relays
- Editable via NIP-07 signed update event

### Multi-Device
- NIP-46 delegated signing (deferred to v2)
- v1: users manage their own keys across devices

---

## 7. Tech Stack

### Decision: **TBD — NQ1 open**

| Layer | Option A | Option B | Option C | Option D |
|-------|----------|----------|----------|----------|
| Frontend | SvelteKit | Next.js 14 | Vite + Solid | Vite + React |
| Backend | SvelteKit SSR | Next.js API | Deno server | Deno server |
| Nostr lib | nostr-tools | NDK | nostr-tools | deno-nostr |
| Database | SQLite | Postgres | Deno KV / Postgres | Deno KV / Postgres |
| Deployment | VPS / Pi | Vercel / VPS | Deno Deploy / VPS | Deno Deploy / VPS |

**Recommended by Clawd:** Option B (Next.js + NDK + Postgres) — best SEO, largest ecosystem.
**Dennis flagged:** Option D (Vite + Deno) — pending Dennis's elaboration.
**Awaiting:** Dennis's tech stack decision.

### Postgres Schema (What Nostr Can't Store)

Nostr is not a database. Postgres is the acceleration layer.

```sql
-- Sessions
CREATE TABLE sessions (
  id TEXT PRIMARY KEY,
  npub TEXT NOT NULL,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  last_seen TIMESTAMPTZ DEFAULT NOW()
);

-- Article cache (latest version only)
CREATE TABLE articles (
  id TEXT PRIMARY KEY,        -- d tag
  author_npub TEXT NOT NULL,
  title TEXT,
  summary TEXT,
  cover_image TEXT,
  published_at TIMESTAMPTZ,
  last_seen_at TIMESTAMPTZ,
  zap_count INTEGER DEFAULT 0,
  featured BOOLEAN DEFAULT FALSE
);

-- Zap totals (aggregated from relay events)
CREATE TABLE zap_totals (
  article_id TEXT PRIMARY KEY REFERENCES articles(id),
  total_msats BIGINT DEFAULT 0,
  last_updated TIMESTAMPTZ
);

-- Moderation queue (featured page)
CREATE TABLE mod_queue (
  article_id TEXT PRIMARY KEY,
  submitted_at TIMESTAMPTZ DEFAULT NOW(),
  status TEXT DEFAULT 'pending',  -- pending / approved / rejected
  reviewed_at TIMESTAMPTZ
);

-- Byline config (per-deployment)
CREATE TABLE config (
  key TEXT PRIMARY KEY,
  value JSONB
);
```

---

## 8. Docker Self-Hosting

### Docker Compose (for self-hosters)

```yaml
services:
  app:
    image: byline/app
    ports: ["3000:3000"]
    env_file: .env
    depends_on: [db]
  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: byline
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes: [pgdata:/var/lib/postgresql/data]
  relay:
    image: byline/relay  # nostr-rs-relay
    ports: ["8080:8080"]
    volumes: [/data/relay:/data]
volumes:
  pgdata:
```

### Configuration (.env)

```env
# Tag namespace — self-hosters can set their own
BYLINE_TAG=byline
BYLINE_TAG_PREFIX=byline

# Database
DATABASE_URL=postgresql://postgres:${DB_PASSWORD}@db:5432/byline

# Relay (optional — for self-hosted relay)
RELAY_URL=wss://relay.yourdomain.com

# Lightning (for zap splits)
BYLINE_LIGHTNING_ADDRESS=byline@yourdomain.com
ZAP_SPLIT_AUTHOR=95
ZAP_SPLIT_PLATFORM=5
```

### Minimum VPS Requirements
- RAM: 2GB
- CPU: 1 core
- Disk: 20GB SSD
- Cost: ~€5-10/month (Hetzner, Contabo, DigitalOcean)

### Raspberry Pi 4
- Viable for light traffic (single-user or small blog)
- Recommended: 4GB RAM model
- Not recommended for relay + app + postgres simultaneously at scale

---

## 9. Content Policy

**Confirmed:** Nostr-level free speech. No platform restrictions beyond what Nostr itself provides.

- Byline does not censor content on the network
- Byline can moderate its own **featured page** (curated selection)
- Featured page: AI reads articles before featuring (NQ4 criteria TBD)
- Reports handled via in-app form → human review
- EU DSA compliance: notice-and-takedown for featured page, appeal path

---

## 10. MVP Scope

**TBD — awaiting Dennis's answer on NQ7**

Questions that gate MVP definition:
- [x] NQ3: Free speech ✅
- [x] NQ5: Latest-only + imports + delete ✅
- [x] NQ8: Alby login + prompt to write ✅
- [x] NQ9: Small zap split ✅
- [ ] **NQ7: MVP feature set** — Dennis: is self-hosted Docker a hard MVP requirement?
- [ ] **NQ1: Tech stack** — pending Dennis decision
- [ ] **NQ2/NQ10: Name** — pending Dennis decision

**Likely MVP features (assuming NQ7 answer):**
- Web reader: front-page + filter page + article view
- Alby login / in-browser keygen
- Markdown article creation + publishing to Nostr (kind 30023)
- Zap button (Alby WebLN)
- Docker self-hosted deployment
- Postgres for caching/acceleration

**Deferred to v2:**
- Lightning zap split configuration
- Featured page AI moderation
- Import from URL
- NIP-46 delegated signing
- Daybook teen variant

---

## 11. Daybook (Teen Variant)

**Status:** Deferred. Self-hosted Docker configuration is confirmed as important.

TBD specifics (NQ6 deferred):
- Separate app vs. mode switch in same app
- Age verification approach
- Guardian visibility mechanism
- Jurisdiction / COPPA approach

---

## 12. Open Questions Summary

| Question | Status | Owner |
|----------|--------|-------|
| NQ1: Tech stack | **OPEN** | Dennis to decide |
| NQ2: Discovery tag | **OPEN** | Dennis to decide |
| NQ4: Featured page curation | **OPEN** | Defer |
| NQ6: Daybook specifics | **OPEN** | Defer |
| NQ7: MVP scope | **OPEN** | Dennis to decide |
| NQ2/NQ10: Name | **OPEN** | Dennis to decide |
| Zap split implementation details | **OPEN** | Defer |
| Alby split configuration | **OPEN** | Defer |

---

## 13. Reference Implementations

- **Habla News** (habla.news) — closest reference: Next.js + NWC + NIP-23
- **YakiHonne** — long-form social on Nostr
- **Wavlake** — creator platform with Lightning
- **Highlighter** — content monetization on Nostr

## 14. Folder Structure

```
byline/
├── SPEC.md              ← you are here
├── PROJECT.md           ← platform overview
├── agent.md             ← research agent instructions
├── questions.md         ← 10 foundation questions (answered)
├── next-questions.md    ← 10 phase-2 questions (partially answered)
├── research/
│   ├── nip-23-longform.md
│   ├── nip-57-lightning-zaps.md
│   ├── relay-landscape.md
│   ├── tech-stack-options.md
│   ├── developer-experience.md
│   ├── alby-integration.md
│   └── moderation-safety.md
└── SPEC.md
```
