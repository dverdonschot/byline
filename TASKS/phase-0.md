# Phase 0 — Foundation

**Goal:** A running project scaffold with all infrastructure in place, ready for feature work.
**Started:** 2026-04-17
**Completed:** ⏳

## Decisions (Locked)

- **Name:** Byline ✅
- **Backend:** None in Phase 0. Frontend queries relays directly with nostr-tools.
- **Database:** None in Phase 0. User preferences (pubkey → settings) deferred until real need.
- **Own relay:** Phase goal. Public relays first. Nostr-tools for all relay communication.
- **Docker:** Dev runs Vite standalone. Docker Compose for production image only.
- **Logo:** Deferred to Phase 4.

## Prerequisites

- [ ] Repo created and pushed to GitHub ✅
- [ ] `PLAN/` folder contains SPEC.md, UX.md, BUILD.md, etc. ✅
- [ ] `TASKS/` folder contains this phase file ✅

---

## Tasks

### 0.1 — Project Scaffold
- [ ] `npm create vite@latest` with React + TypeScript template
- [ ] Clean default Vite boilerplate (remove unused CSS, components, assets)
- [ ] Verify `npm run dev` starts without errors
- [ ] Add `package.json` scripts: `dev`, `build`, `preview`, `lint`
- [ ] Add `.gitignore` (node_modules, dist, .env, etc.)
- [ ] Initial commit: "feat: project scaffold with Vite + React + TypeScript"

### 0.2 — Design Tokens (Gulf Blue Palette)
- [ ] Create `src/styles/tokens.css` with all CSS custom properties from SPEC.md
- [ ] Light mode variables at `:root`
- [ ] Dark mode variables at `[data-theme="dark"]`
- [ ] Create `src/styles/global.css` (reset, base typography, body bg)
- [ ] Import tokens.css and global.css in `src/main.tsx`
- [ ] Create `src/styles/components.css` for shared component base styles
- [ ] Verify: toggle `data-theme="dark"` on `<html>` element renders correct dark palette

### 0.3 — CSS Modules Setup
- [ ] Configure Vite for CSS Modules (default: `*.module.css` files)
- [ ] Create `src/components/.gitkeep` as placeholder
- [ ] Create `src/styles/.gitkeep` as placeholder
- [ ] Document CSS Modules convention: `ComponentName.module.css` co-located with component
- [ ] Create one minimal example component to prove CSS Modules work (e.g., `Logo` component)

### 0.4 — TypeScript Types
- [ ] Create `src/types/nostr.ts` — Nostr event types (kind 0, 1, 5, 30023, 30024, 1111, 9734, 9735)
- [ ] Create `src/types/api.ts` — all API request/response shapes
- [ ] Create `src/types/session.ts` — Session, RelayStatus, Toast interfaces
- [ ] Create `src/types/index.ts` — barrel export
- [ ] Verify: `tsc --noEmit` passes with zero errors

### 0.5 — Nostr Relay Layer (nostr-tools)
- [ ] Install `nostr-tools` and `nostr-hooks` (or write minimal custom hooks)
- [ ] Create `src/lib/relayPool.ts` — manages multiple relay connections, handles reconnection
- [ ] Create `src/lib/kinds.ts` — constants for all Nostr kind numbers used by Byline
- [ ] Create `src/hooks/useNostr.ts` — `useNostr()` hook: connects extension, returns pubkey/nip04
- [ ] Create `src/hooks/useArticles.ts` — fetches and caches kind 30023 articles from relays
- [ ] Verify: connect to 4 public relays, fetch one article, render it on page

### 0.6 — Docker Production Build
- [ ] Create `Dockerfile` — builds Vite app, serves static files with nginx (no backend)
- [ ] Create `docker/docker-compose.yml` — single `app` service, no postgres/redis in dev
- [ ] Add `docker/.env.example` documenting required env vars (none for MVP)
- [ ] Verify: `docker build` produces image, `docker compose up` serves app on port 3000
- [ ] Add healthcheck to app container

### 0.7 — Dev Workflow
- [ ] `npm run dev` starts Vite on :5173 with hot reload (no backend needed)
- [ ] Add `npm run docker:up` and `npm run docker:down` scripts
- [ ] Write `CONTRIBUTING.md` documenting the dev workflow

### 0.8 — End-to-End Verification
- [ ] App loads at `http://localhost:5173` — blank page with correct Gulf Blue background
- [ ] Dark mode toggle works (switches `data-theme` on `<html>`)
- [ ] Nostr extension connects and pubkey is detected
- [ ] Articles load from public relays (mock data if no extension — until Phase 1)
- [ ] GitHub Actions CI passes (builds Docker image)

---

## Exit Criteria (Phase 1 Unlocks When)

- [ ] `npm run dev` works with hot reload (no backend required)
- [ ] Docker image builds and serves on port 3000
- [ ] `tsc --noEmit` passes in `src/`
- [ ] GitHub Actions CI passes
- [ ] Relay pool connects to public relays and fetches kind 30023 events