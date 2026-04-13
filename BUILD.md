# Longform — Build Plan

**Version:** 0.1
**Last updated:** 2026-04-13
**Status:** Planning — Dennis to review and approve

---

## 1. What Are We Building?

A self-hosted long-form blogging platform. Writers publish markdown stories to Nostr. Readers browse, search, and zap via Lightning. Built as a Docker app you run on your own VPS.

---

## 2. MVP Feature List

### Must Have (MVP)

| # | Feature | Description |
|---|---------|-------------|
| 1 | **Landing / Front Page** | Shows featured/recent stories, freshest first |
| 2 | **Story View** | Renders markdown article from Nostr kind 30023 event |
| 3 | **Filter / Browse Page** | Filter by tag, by author, search |
| 4 | **Write / Publish** | Markdown editor, preview, publish to Nostr |
| 5 | **Login with Nostr** | Alby extension OR in-browser key → NIP-07 signing |
| 6 | **Zap Button** | Reader zaps author via Alby WebLN |
| 7 | **Author Profile** | Show name, bio, avatar from Nostr kind-0 |
| 8 | **Import Post** | Paste markdown → publish as kind 30023 |
| 9 | **Delete Post** | Send Nostr kind 5 delete event |
| 10 | **Self-Hosted Docker** | docker-compose up, works on your VPS |

### Nice to Have (Post-MVP)

| # | Feature |
|---|---------|
| A | Featured Page (AI-curated selection) |
| B | Zap Split (platform cut) |
| C | Import from URL |
| D | Multi-language support |
| E | Custom theme / branding |

---

## 3. Tech Stack (Confirmed)

```
Frontend:    Vite + TypeScript + React 18 (or Solid.js)
Styling:    Plain CSS with CSS custom properties (no Tailwind unless asked)
Markdown:   marked.js (fast, simple)
Nostr lib:  nostr-tools (or deno-nostr for Deno backend)
Zaps:       window.webln (Alby WebLN)
Backend:    Deno HTTP server (fresh.deno.dev or custom)
Database:   Postgres (via Deno postgres driver)
Auth:       NIP-07 (browser extension) + in-browser keygen fallback
Deployment: Docker Compose
```

**Frontend framework question:** React or Solid.js?
- React: most familiar, biggest ecosystem
- Solid.js: lighter, faster, less boilerplate
- Both work with Vite. Dennis — preference?

---

## 4. Pages / Routes

| Route | Page | Auth needed? |
|-------|------|-------------|
| `/` | Front page — recent + featured stories | No |
| `/story/:naddr` | Individual article view | No |
| `/filter` | Browse — filter by tag / author | No |
| `/login` | Login / connect Nostr identity | No (landing here) |
| `/write` | New story editor | Yes |
| `/story/:naddr/edit` | Edit existing story | Yes (must be author) |
| `/profile/:npub` | Author profile page | No |
| `/settings` | Account settings (Lightning address, display name) | Yes |

---

## 5. Frontend Architecture

```
src/
├── main.tsx              # Vite entry point
├── App.tsx               # Router + layout shell
├── pages/
│   ├── Home.tsx          # /
│   ├── Story.tsx         # /story/:naddr
│   ├── Filter.tsx        # /filter
│   ├── Write.tsx         # /write
│   ├── Edit.tsx          # /story/:naddr/edit
│   ├── Login.tsx         # /login
│   ├── Profile.tsx       # /profile/:npub
│   └── Settings.tsx      # /settings
├── components/
│   ├── StoryCard.tsx     # Preview card on home/filter
│   ├── MarkdownRenderer.tsx
│   ├── ZapButton.tsx
│   ├── NostrProfile.tsx  # Avatar + name + npub
│   ├── TagBadge.tsx
│   ├── MarkdownEditor.tsx # Textarea + preview toggle
│   ├── RelayStatus.tsx   # Shows connected relays
│   └── NavBar.tsx
├── lib/
│   ├── nostr.ts          # nostr-tools wrappers (query, publish, sign)
│   ├── relay.ts          # Relay pool management
│   ├── zap.ts            # WebLN zap flow
│   ├── markdown.ts       # marked.js setup
│   ├── db.ts             # Postgres queries (via Deno backend)
│   └── types.ts          # Shared TypeScript types
├── styles/
│   ├── global.css        # Reset, CSS variables, typography
│   ├── components.css    # Component-level styles
│   └── pages.css         # Page-level styles
└── stores/
    └── session.ts        # Current user session (npub, kind-0 profile)
```

**State management:** No Redux. React Context for session, local state for components. Solid.js alternative: signals.

---

## 6. CSS Strategy

Simple and clean. No Tailwind unless Dennis prefers it.

```
CSS Variables (in :root):
  --color-bg: #faf9f7        (warm off-white)
  --color-surface: #ffffff
  --color-border: #e8e4df
  --color-text: #1a1a1a
  --color-text-muted: #6b6b6b
  --color-accent: #d4a574     (warm amber/gold — lightning color)
  --color-accent-dark: #b8956a
  --font-body: Georgia, serif (editorial, writerly)
  --font-ui: system-ui, sans-serif

Typography:
  Body: 18px / 1.7 line-height
  Headings: Georgia serif
  UI elements: system-ui
  Code: monospace
```

**Design inspiration:** Medium meets Nostr — clean editorial layout, warm tones, serif headings, generous whitespace. Not dark-mode-first.

---

## 7. Backend Architecture (Deno)

Since we're using Deno as the backend runtime:

```
backend/
├── main.ts               # Deno server entry
├── fresh/               # Or: custom HTTP router
├── routes/
│   ├── api/
│   │   ├── articles.ts   # GET /api/articles?tag=&author=
│   │   ├── article.ts    # GET /api/article/:naddr
│   │   ├── profile.ts    # GET /api/profile/:npub
│   │   ├── publish.ts    # POST /api/publish (signed event from frontend)
│   │   ├── zaps.ts       # GET /api/zaps/:articleId
│   │   └── relay.ts      # WebSocket relay proxy (optional)
│   └── pages/
│       └── [...].tsx     # Server-rendered pages (optional SSR)
├── db/
│   ├── schema.sql        # Postgres schema
│   └── queries.ts        # SQL query functions
├── nostr/
│   ├── relay.ts          # Relay pool
│   ├── cache.ts          # Write-through cache: Nostr → Postgres
│   └── sign.ts           # NIP-07 proxy (if needed)
└── docker/
    └── entrypoint.sh
```

**Key question:** Does the frontend query relays directly (from browser), or via the Deno backend?

- **Option A (Direct):** Browser queries relays via nostr-tools WebSocket. Fast, no backend needed for reads. But CORS issues with some relays.
- **Option B (Via backend):** Deno backend queries relays, caches in Postgres, serves to frontend. Better caching, consistent relay list, harder to scale.
- **Option C (Hybrid):** Read direct from browser for real-time data; write through backend for cache warming.

**Recommended: Option A (Direct)** for MVP — keep it simple. Frontend queries relays directly. Backend only for: Postgres cache writes, publishing (proxy signed events), and zap handling.

---

## 8. Nostr Data Flow

### Reading a story
```
User opens /story/:naddr
  → Frontend decodes naddr → [kind, pubkey, d-tag, relay]
  → nostr-tools queries 4 relays in parallel
  → First valid kind 30023 response wins
  → marked.js renders markdown
  → ZapButton queries for kind 9735 events (zap count)
```

### Publishing a story
```
User clicks "Publish" in /write
  → Frontend builds kind 30023 event
  → window.nostr.signEvent(event) — Alby/nos2x signs
  → POST /api/publish (backend receives signed event)
  → Backend verifies signature
  → Backend publishes to 4 relays in parallel
  → Backend writes to Postgres cache
  → Returns success → redirect to /story/:naddr
```

### Caching (Postgres)
```
On publish:  write to articles table
On read:    check Postgres first, then relay, update cache
On delete:  mark deleted in Postgres (don't show)
```

---

## 9. Docker Compose Structure

```yaml
services:
  app:
    build: .
    ports: ["3000:3000"]
    env_file: .env
    depends_on: [db]
    networks: [byline]

  db:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: longform
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes: [pgdata:/var/lib/postgresql/data]
    networks: [byline]

  # Optional: own relay for discovery
  relay:
    image: scsibug/nostr-rs-relay:latest
    ports: ["8080:8080"]
    volumes: [/data/relay:/data]
    networks: [byline]

networks:
  byline:
volumes:
  pgdata:
```

**Note:** The app talks to Nostr directly from the browser for reads. The optional relay is for self-hosters who want their own relay.

---

## 10. Build Order

We build in layers — each layer produces something usable.

### Layer 1: Skeleton (no Nostr yet)
- [ ] Vite + React project scaffolded
- [ ] CSS variables + global styles
- [ ] Router setup (home, story, filter, write, login)
- [ ] NavBar + basic layout
- [ ] Docker Compose + Postgres schema
- [ ] "Hello World" page in Docker ✅

**Deliverable:** Running Docker app on Tailscale VPS, blank home page visible

### Layer 2: Read (no auth yet)
- [ ] Relay query (nostr-tools, from browser)
- [ ] Story view — render kind 30023 event
- [ ] Home page — list stories from relays
- [ ] Filter page — query by tag
- [ ] Profile page — show kind-0 metadata
- [ ] Markdown rendering (marked.js)

**Deliverable:** Read any public Nostr long-form story without logging in

### Layer 3: Write (auth)
- [ ] NIP-07 signing (Alby / nos2x detect + use)
- [ ] In-browser keygen fallback
- [ ] Markdown editor with preview
- [ ] Publish → backend → relays
- [ ] Edit (new kind 30023 with same d-tag)
- [ ] Delete (kind 5 event)

**Deliverable:** Publish a story to Nostr from the web UI

### Layer 4: Zap
- [ ] Zap button (window.webln)
- [ ] Zap count display (query kind 9735)
- [ ] Zap split (NIP-57 multi-p, if MVP)
- [ ] Author Lightning address display

**Deliverable:** Zap an author from a story page

### Layer 5: Polish
- [ ] Settings page (Lightning address in profile)
- [ ] Import paste
- [ ] Responsive design check
- [ ] Error states (relay failures, signing denied)
- [ ] Loading states + skeleton screens

**Deliverable:** Shareable with others

---

## 11. Open Decisions

These need Dennis's input before Layer 1 can start:

| Decision | Options | Status |
|----------|---------|--------|
| Frontend framework | React vs Solid.js | **Dennis to choose** |
| Frontend queries relays directly? | Yes (simpler) vs via backend | **Recommended: Direct** |
| CSS approach | Plain CSS + variables vs Tailwind | **Recommended: Plain CSS** |
| Own relay in docker? | Yes (optional) vs relay-free MVP | **Recommended: Optional** |
| Deno framework | Fresh.deno.dev vs custom HTTP | **Recommended: Fresh** |

---

## 12. What Dennis Needs to Decide Now

1. **React or Solid.js?** — React more familiar, Solid lighter
2. **Tailwind or plain CSS?** — Tailwind faster to prototype, plain CSS more ownable
3. **Anything else from this plan that needs changing?**

Once Dennis approves this plan → Layer 1 starts.
