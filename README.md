# Byline

Long-form publishing on Nostr. Writers publish markdown stories; readers discover them via topic tags and zap with Lightning.

**Tech:** Vite + React + TypeScript · Express + Drizzle + Postgres + Redis · Docker

---

## Project Status

| Item | Status |
|------|--------|
| Research | ✅ Complete |
| Architecture | ✅ Confirmed |
| Color + Design | ✅ Gulf Blue palette |
| Phase 0 (Foundation) | ⏳ Not started |

See [TASKS/PHASES.md](TASKS/PHASES.md) for full task breakdown.

---

## Key Docs

| Doc | What it's for |
|-----|---------------|
| [PLAN/SPEC.md](PLAN/SPEC.md) | Technical specification — source of truth |
| [PLAN/UX.md](PLAN/UX.md) | UX deep dive, routes, component inventory |
| [PLAN/BUILD.md](PLAN/BUILD.md) | Feature inventory, API design, component specs |
| [PLAN/SHIPPING.md](PLAN/SHIPPING.md) | Pre-launch checklist |
| [TASKS/PHASES.md](TASKS/PHASES.md) | **Start here for task work** |
| [PLAN/agent.md](PLAN/agent.md) | Coding agent instructions |

---

## Quick Start

```bash
# Phase 0 sets up the dev environment
# See TASKS/phase-0.md for full instructions
```

Requirements: Node.js 20+, Docker, Docker Compose

---

## Design System

Gulf Blue anchor color — teal-tinted platform identity.
Orange reserved for Lightning payment moments.

```css
/* Light mode — see PLAN/BUILD.md for full token list */
--color-accent:     #008891;   /* Gulf Blue */
--color-lightning:   #f59e0b;   /* Lightning orange */
```

Dark mode supported (toggle in UI, persisted in localStorage).
