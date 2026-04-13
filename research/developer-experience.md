# Research: Developer Experience & Self-Hosting (Q10)

**Status:** [CONFIRMED — architecture clear]

## Two User Journeys

### Journey 1: Create and publish a story via Byline
1. User logs in via Alby (NWC/NIP-07 — browser popup to connect wallet)
2. Byline generates keypair in-browser (first time) OR uses existing Nostr identity from Alby extension
3. User writes in Markdown editor (client-side)
4. User sets tags (e.g., `byline`, `byline/dennis`, topic tags)
5. Byline creates kind 30023 event, sends to NIP-07 signing (Alby popup: "Sign this event?")
6. Event published to 4 public relays
7. Byline caches event in Postgres (for fast reads, SEO)

### Journey 2: "Link an external blog to Nostr via Byline"
This is actually Journey 1 — the user pastes/imports their existing markdown content into Byline's editor, then Byline publishes it to Nostr as kind 30023. The content lives on Nostr; Byline becomes the bridge.

**Note:** If the external blog already exists on a different Nostr tag, Byline can display it via relay queries without re-publishing. But for Byline to "own" the content, it must publish kind 30023 events.

## Docker Self-Hosting

### Option A: Byline + nostr-rs-relay (recommended for self-hosters)

```bash
# Clone Byline
git clone https://github.com/byline/byline-docker.git
cd byline-docker

# Edit .env — set your tag namespace (e.g., byline_cooking instead of byline)
cp .env.example .env
nano .env

# Spin up
docker-compose up -d
```

**What you get:**
- Byline Next.js app (SSR)
- nostr-rs-relay (Rust relay, SQLite/Postgres)
- Postgres (for Byline cache)
- Nginx (reverse proxy)

**Requirements:** 2GB RAM, 20GB disk, any VPS or Raspberry Pi 4

### Option B: Byline app only (connect to public relays)

```bash
# Just the app, uses public relays for storage
docker run -p 3000:3000 byline/byline-app
```

### Docker Images Needed
1. `byline/app` — Next.js app (Dockerfile with node:20-alpine)
2. `byline/relay` — nostr-rs-relay with config (optional, for self-hosters)
3. `postgres:16` — for Byline cache/state
4. `nginx` — reverse proxy / SSL termination

## NWC Login Flow (Alby — automatic Nostr login)

This is the cleanest UX for non-technical writers:

```
1. User clicks "Login with Alby"
2. Byline calls window.alby.enable() — browser popup to connect Alby wallet
3. Alby returns: { nostrPubkey, walletBalance, lnurl }
4. Byline uses window.nostr.signEvent() when publishing (Alby signs the kind 30023 event)
5. User never sees raw keys
```

**Fallback for users without Alby:**
- nos2x extension (NIP-07, no Lightning)
- Generate keypair in-browser, warn user to back it up

## PostgreSQL State (what Nostr can't store)

Nostr is not a database. Postgres stores:

| Data | Why in Postgres | Nostr equivalent |
|------|----------------|-----------------|
| User sessions | Fast, server-side auth | N/A |
| Article cache (latest version) | Fast reads, SEO | kind 30023 on relay |
| User preferences | Per-user settings | kind 30000? (not standard) |
| Moderation queue | Featured page AI reads | N/A |
| Zap totals | Aggregated from 9735 events | Kind 9735 on relay |
| Featured article list | Curated, not on Nostr | NIP-51? (custom) |
| Tag → article index | Fast filter queries | Relay has raw events |

**Design principle:** Postgres is a cache/acceleration layer. The source of truth is always the Nostr relay. If relay data conflicts with Postgres, relay wins.

## VPS Requirements for Byline hosted version

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| RAM | 2 GB | 4 GB |
| CPU | 1 core | 2 cores |
| Disk | 20 GB | 50 GB SSD |
| OS | Ubuntu 22.04 | Ubuntu 22.04 |
| Docker | 24.x | latest |

**Cost:** ~€5-10/month (Hetzner, Contabo, DigitalOcean)

## Raspberry Pi Viable?

Yes — RPi 4 with 4GB RAM can run:
- Byline app (light traffic)
- nostr-rs-relay (light traffic)
- Postgres (small dataset)

At scale, RPi becomes limiting (relay needs disk I/O for event queries).

## Federation Between Byline Instances

Since Byline instances all publish to the same public relays, content automatically federates:
- Instance A publishes to relay.damus.io + nos.lol
- Instance B queries the same relays and sees Instance A's content
- No special federation protocol needed — Nostr handles it

**For private tags:** Instance A could use a private relay (self-hosted), Instance B can't see it.

## Reference Implementations (existing Nostr blogs)

- **Habla News** (habla.news) — blogging platform on Nostr, NWC for zaps
- **YakiHonne** (yakihonne.com) — long-form social, also NWC
- **Wavlake** — creator platform, Nostr + Lightning
- **Highlighter** — content monetization on Nostr

Habla News is the closest reference for Byline's blogging use case.
