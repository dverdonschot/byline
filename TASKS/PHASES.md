# Byline — Phase Overview

Master task index. Always check here first before starting any work.

## Phase Status

| Phase | Name | Status | Last Updated |
|-------|------|--------|-------------|
| 0 | Foundation | ⏳ Not started | 2026-04-17 |
| 1 | Read | 🔒 Locked | — |
| 2 | Write | 🔒 Locked | — |
| 3 | Zap | 🔒 Locked | — |
| 4 | Polish | 🔒 Locked | — |
| 5 | Platform | 🔒 Locked | — |

## Unlock Rule
Each phase unlocks when the previous phase's every task is marked **done**.

## Quick Summary

- **Phase 0** — Project scaffold: Vite, TypeScript, CSS tokens, Docker Compose, Postgres schema
- **Phase 1** — Read path: NavBar, Home, Article, Filter, Author, MarkdownRenderer, relay queries
- **Phase 2** — Write path: Login, Editor, Publish, Edit, Delete, Settings
- **Phase 3** — Zap: ZapButton, WebLN flow, zap receipts, ZapCount
- **Phase 4** — Polish: Responsive, SEO, RSS, share, word count, related articles
- **Phase 5** — Platform: Featured page, moderation, sitemap, cache cron

## Task File Convention

Each phase file (`phase-N.md`) follows this format:

```markdown
## Phase N: <Name>

**Goal:** <one sentence>
**Started:** YYYY-MM-DD
**Completed:** YYYY-MM-DD or ⏳

### Tasks

- [ ] <task> — <why it matters>
```

When a task is done: update `[ ]` → `[x]`, add a line noting what was done.
When all tasks are done: update phase status in PHASES.md and unlock next phase.
