# Research: Tech Stack Options

**Status:** [RESEARCHING — 3 options identified, recommendation pending]

## 3 Main Stack Options

---

### Option A: SvelteKit + nostr-tools + SQLite

**Frontend:** SvelteKit (SSR + client-side hydration)
**Nostr lib:** nostr-tools (most mature JS Nostr library)
**Database:** SQLite (via better-sqlite3 or libsql)
**Lightning:** Alby API / WebLN
**Image hosting:** External (nostr.build, imgproxy, or self-hosted)

```
SvelteKit app
  ├── nostr-tools (relay queries, event signing)
  ├── marked / remark (markdown rendering)
  ├── WebLN (Alby browser extension for zaps)
  └── SQLite (relay data cache, user sessions)
```

**Pros:**
- SvelteKit is lightweight, fast — excellent for content sites
- nostr-tools is the most battle-tested JS library for Nostr
- SQLite is zero-config, perfect for single-server deployment
- Can run on Raspberry Pi
- Shugur Relay itself uses SvelteKit — proven at scale

**Cons:**
- Svelte has smaller ecosystem than React
- SSR with Nostr requires careful handling (WebSocket relay queries)
- No built-in caching layer

**Best for:** Lightweight deployment, fast pages, cheap hosting

---

### Option B: Next.js + NDK + Postgres

**Frontend:** Next.js 14 (App Router, SSR)
**Nostr lib:** @nostr-dev-kit/ndk (higher-level abstractions, caching, outbox model)
**Database:** Postgres (for relay data caching, user profiles, article metadata)
**Lightning:** Alby + LNURL
**Image hosting:** External + NIP-94

```
Next.js app
  ├── NDK (@nostr-dev-kit/ndk) — relay pool, event cache, outbox
  ├── Postgres (cache + metadata)
  ├── marked / remark (markdown rendering)
  └── WebLN / Alby SDK (zaps)
```

**Pros:**
- React ecosystem — largest library support, easiest to hire
- NDK handles multi-relay complexity, caching, outbox model
- Next.js SSR = great SEO (important for content discovery)
- Postgres is battle-tested for production workloads
- Vercel/Netlify deploys easily

**Cons:**
- Heavier stack — more moving parts
- Postgres requires managed hosting or Docker
- More complex local development setup
- Costs more to run (managed Postgres)

**Best for:** SEO-heavy content platform, larger team, Vercel deployment

---

### Option C: Bun + Hono + nostr-tools + SQLite

**Backend:** Bun (JS runtime — fast, lightweight)
**Framework:** Hono (lightweight SSR framework, works with any view library)
**Frontend:** Vanilla JS +htm or Solid.js
**Nostr lib:** nostr-tools (pure JS)
**Database:** SQLite (libsql/Turso for edge-compatible DB)
**Lightning:** Alby SDK

```
Bun server (Hono)
  ├── nostr-tools (relay queries)
  ├── SQLite (article cache, sessions)
  ├── NIP-94 file handler (image uploads)
  └── API routes → rendered HTML
```

**Pros:**
- Bun is significantly faster than Node.js
- Hono is the fastest JS web framework
- SQLite via Turso = can deploy to edge (Cloudflare Workers, etc.)
- Minimal footprint — cheapest VPS works fine

**Cons:**
- Bun is newer — some edge cases may not be solved
- Smaller ecosystem, harder to get help
- Requires more custom wiring

**Best for:** Edge deployment, minimal cost, maximum speed

---

## Clawd's Recommendation: **Option B (Next.js + NDK + Postgres)**

**Why:**
1. **SEO matters** — Byline is a content discovery platform. SSR + good meta tags = Google can index stories
2. **NDK's outbox model** solves the "which relay has my data" problem automatically
3. **Next.js has the largest ecosystem** — any library you need exists for React
4. **Dennis's existing project** (ZwermAI) is Next.js — familiarity advantage
5. **Alby has a Next.js SDK** (via WebLN) — Lightning integration is clean

**But:** If Dennis prefers lightweight and cheap, Option A (SvelteKit) is a very strong second.

---

## Tech Stack Summary Table

| Layer | Option A | Option B (recommended) | Option C |
|-------|----------|------------------------|----------|
| Framework | SvelteKit | Next.js 14 | Bun + Hono |
| Nostr lib | nostr-tools | NDK | nostr-tools |
| Database | SQLite | Postgres | SQLite/Turso |
| Lightning | WebLN/Alby | WebLN/Alby | WebLN/Alby |
| Deployment | VPS / Pi | Vercel / VPS | Edge / VPS |
| Nostr signing | NIP-07 (extension) | NIP-07 (extension) | NIP-07 (extension) |
| Markdown | marked / remark | marked / remark | marked / remark |
| Images | External URL | External URL | External URL |

## Immediate Dependencies

For all options:
- **Signing:** NIP-07 browser extension (nos2x or Alby) — Byline never touches private keys
- **Lightning:** Author sets Lightning Address in Nostr profile (lud16 field) — no Byline server needed
- **Markdown:** marked.js (fast, sufficient) or unified/remark (more control)
- **Image hosting:** Recommend nostr.build for quick start, self-hosted for production
