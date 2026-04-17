# Phase 0 — Foundation

**Goal:** A running project scaffold with all infrastructure in place, ready for feature work.
**Started:** 2026-04-17
**Completed:** ⏳

## Prerequisites

- [ ] Repo created and pushed to GitHub
- [ ] `PLAN/` folder contains SPEC.md, UX.md, BUILD.md, etc.
- [ ] `TASKS/` folder contains this phase file

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

### 0.5 — Express Backend Scaffold
- [ ] Create `server/` directory with `package.json`
- [ ] Install: express, cors, dotenv, drizzle-orm, pg, redis, nostr-tools
- [ ] Create `server/src/index.ts` — minimal Express app that responds "ok" on `/health`
- [ ] Create `server/src/db/schema.ts` — Drizzle schema matching SPEC.md Postgres schema
- [ ] Create `server/src/db/index.ts` — DB connection (reads DATABASE_URL from env)
- [ ] Create `server/src/redis.ts` — Redis connection (reads REDIS_URL from env)
- [ ] Create `server/.env.example` with all required vars (DATABASE_URL, REDIS_URL, etc.)
- [ ] Verify: `tsc --noEmit` passes in server/

### 0.6 — Docker Compose (3 Containers)
- [ ] Create `docker/docker-compose.yml` — app + postgres:16-alpine + redis:7-alpine
- [ ] Create `Dockerfile` for app (builds Vite app, runs Express server in production)
- [ ] Create `docker/.env` (DATABASE_URL pointing to `db:5432`, REDIS_URL pointing to `redis:6379`)
- [ ] Add healthcheck to postgres service
- [ ] Add healthcheck to redis service
- [ ] Add depends_on + condition: service_healthy to app container
- [ ] Add `restart: on-failure` to all services
- [ ] Volume mounts: pgdata, redisdata
- [ ] Verify: `docker compose config` passes with zero errors

### 0.7 — Drizzle Migrations
- [ ] Add `drizzle-kit` config to server (`drizzle.config.ts`)
- [ ] Write initial migration for all tables in SPEC.md schema
- [ ] Create `docker/init.sql` — runs on postgres first start (creates extensions if needed)
- [ ] Add migration runner to server startup (on boot, run pending migrations)
- [ ] Verify: containers start clean with `docker compose down -v && docker compose up -d`

### 0.8 — Dev Workflow (Hot Reload)
- [ ] Add `concurrently` to root `package.json` to run Vite + Express together in dev
- [ ] Root `npm run dev` starts: Vite on :5173 + Express on :3000 with hot reload
- [ ] Add `npm run docker:up` and `npm run docker:down` scripts
- [ ] Add `npm run db:migrate` script
- [ ] Write `CONTRIBUTING.md` documenting the dev workflow (2 commands to get running)

### 0.9 — End-to-End Verification
- [ ] App loads at `http://localhost:5173` — blank page with correct Gulf Blue background
- [ ] `/health` endpoint returns 200 from running Express server
- [ ] Postgres is reachable from Express (test with a simple query)
- [ ] Redis is reachable from Express (test with PING)
- [ ] Docker Compose starts all 3 containers and they report healthy
- [ ] Push to GitHub — CI should build the Docker image successfully

---

## Exit Criteria (Phase 1 Unlocks When)

- [ ] `npm run dev` works with hot reload
- [ ] All 3 Docker containers start and are healthy
- [ ] Database migrations run automatically on startup
- [ ] `tsc --noEmit` passes in both `src/` and `server/`
- [ ] GitHub Actions CI passes (builds Docker image)
