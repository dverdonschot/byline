# Shipping Checklist

Run this before each commit, PR, and deployment.

## Every Commit
- [ ] `pnpm build` passes (no TS errors)
- [ ] `pnpm test` passes (unit tests)
- [ ] Docker build succeeds (`docker compose build`)
- [ ] No hardcoded secrets or localhost-only URLs in committed code

## Phase Ship Checklist

### Phase 0 — Foundation
- [ ] Vite + React + TypeScript project builds
- [ ] Docker Compose starts app + postgres
- [ ] Database migrations run on startup
- [ ] `pnpm test` green

### Phase 1 — Read
- [ ] Home page loads from Postgres cache
- [ ] Article page loads from relays directly
- [ ] Filter page filters by tag and author
- [ ] Author profile shows name, bio, stories
- [ ] ZapCount shows sats from kind 9735
- [ ] Markdown renders correctly (headings, code, images, blockquotes)
- [ ] No relay errors in browser console

### Phase 2 — Write
- [ ] Alby login works
- [ ] In-browser keygen login works
- [ ] Publish kind 30023 to relay (verify on https://njump.me)
- [ ] Edit updates article (same d-tag, new created_at)
- [ ] Delete sends kind 5 (verify gone from relay)
- [ ] Draft auto-saves to localStorage
- [ ] Profile updates propagate to relays

### Phase 3 — Zap
- [ ] Zap button opens Alby
- [ ] Zap payment succeeds (test with 1 sat)
- [ ] Zap count updates after payment
- [ ] Zap split % reaches platform address (if enabled)

### Pre-Production
- [ ] `.env` is complete (use `.env.example` as template)
- [ ] Relay list is configurable, not hardcoded
- [ ] Postgres connection uses env vars
- [ ] HTTPS works (Tailscale or Cloudflare tunnel)
- [ ] Docker healthchecks pass
- [ ] No CORS errors from relay queries
