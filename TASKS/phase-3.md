# Phase 3 — Zap (Lightning)

**Goal:** Readers can send Lightning zaps to authors. Authors receive sats.
**Started:** ⏳
**Completed:** ⏳

## Prerequisites
- Phase 2 fully complete

---

## Tasks

### 3.1 — ZapButton Component
- [ ] Props: `npub`, `naddr`, `authorLud16`
- [ ] States: idle, loading (spinner), success ("⚡ Zapped!"), error (red, shows message)
- [ ] If no WebLN: show "⚡ Get Alby" linking to https://getalby.com
- [ ] If author no Lightning address: tooltip "Author hasn't set up Lightning yet"
- [ ] Size variants: `sm` (inline with zap count), `md` (default), `lg` (article page prominent)
- [ ] On click: call `window.webln.enable()` → Alby handles invoice + payment

### 3.2 — Zap Amount Presets
- [ ] Four preset buttons: ⚡ 100 / ⚡ 500 / ⚡ 1000 / ⚡ 5000 sats
- [ ] On mobile: horizontally scrollable row
- [ ] Custom amount input (optional, deferred to Phase 4)
- [ ] Zap split: 95% author / 5% Byline (NIP-57 multi-p tag with weights)

### 3.3 — Zap Flow (WebLN + NIP-57)
- [ ] Build kind 9734 zap request event with: `p` (author pubkey), `e` (article event id), `a` (event coordinate), `amount` (msats), relays
- [ ] Sign with NIP-07: `window.nostr.signEvent(zapEvent)`
- [ ] Call `window.webln.makeInvoice({ amount: msats, defaultDescription: 'Zap for...' })`
- [ ] Send zap request event to relays
- [ ] Watch for kind 9735 receipt from author's lnurl server
- [ ] Update ZapCount on article immediately after successful zap

### 3.4 — ZapCount Component
- [ ] Props: `zapCount` (total sats), `zapperCount` (number of zappers)
- [ ] Format: "⚡ 420 sats from 12 zappers"
- [ ] Shown on StoryCard, ArticleCard, and Article page
- [ ] Queries kind 9735 events from relays
- [ ] Aggregates from Redis cache if available (backend-computed)

### 3.5 — Author ZapSection (Profile Page)
- [ ] On author profile: show total sats received across all articles
- [ ] "⚡ 12,450 sats received" prominent badge
- [ ] Breakdown per article in the author's article grid

### 3.6 — Backend: Zap APIs
- [ ] `GET /api/zaps/:articleId` — return aggregated zap count + zapper count from Postgres
- [ ] `POST /api/zaps` — webhook endpoint for receiving zap receipts (kind 9735 events)
- [ ] Backend watches relays for kind 9735 (or receives via webhook from Alby/custom lnurl)
- [ ] Write zap events to Postgres `zaps` table, update `articles.zap_count`
- [ ] Invalidate Redis zap cache on new zap

---

## Exit Criteria (Phase 4 Unlocks When)
- [ ] Clicking ZapButton opens Alby, payment completes, zap receipt appears on Nostr
- [ ] ZapCount updates immediately after successful zap
- [ ] Author's total zap count increases on their profile page
- [ ] Zap counts visible on article cards and article pages
- [ ] Zap split (5% Byline) correctly encoded in zap request
