# agent.md — Byline Coding Agent

## First Thing: Read TASKS/PHASES.md

Before writing any code, open `TASKS/PHASES.md` and check:
- Which phase is currently active
- Which phase file to consult for task details
- What the unlock criteria are

**Never start work without knowing which phase you're in.**

---

## Repo Structure

```
PLAN/           ← Decision documents (read only — don't modify unless asked)
  SPEC.md       ← Technical specification (source of truth)
  UX.md         ← UX deep dive, route map, component inventory
  BUILD.md      ← Feature inventory, component props/states, API design
  PROJECT.md    ← Platform overview
  SHIPPING.md   ← Shipping checklist
  research/     ← Nostr NIP research notes
  agent.md      ← this file

TASKS/          ← Task tracking (update as you go)
  PHASES.md     ← Master phase index — check here first
  phase-0.md    ← Phase 0 tasks (Foundation)
  phase-1.md    ← Phase 1 tasks (Read)
  phase-2.md    ← Phase 2 tasks (Write)
  phase-3.md    ← Phase 3 tasks (Zap)
  phase-4.md    ← Phase 4 tasks (Polish)
  phase-5.md    ← Phase 5 tasks (Platform)

src/            ← Frontend source (created in Phase 0)
server/         ← Backend source (created in Phase 0)
docker/         ← Docker setup (created in Phase 0)
```

---

## Locked Decisions (Do Not Revisit)

| Decision | Value |
|----------|-------|
| Discovery tag | `longform` |
| Tech stack | Vite + React + TypeScript (frontend), Express + Drizzle + Postgres (backend) |
| CSS approach | CSS Modules per component |
| Color palette | Gulf Blue (#008891 light / #00b0c1 dark) — orange for Lightning moments |
| Containers | 3: app + postgres:16-alpine + redis:7-alpine |
| Relay strategy | Browser queries 4 public relays directly via nostr-tools |
| Nostr event kinds | 0, 1, 5, 30023, 30024, 1111, 9734, 9735 |

---

## How to Work in a Phase

1. Read the relevant `phase-N.md` file
2. Pick a task (top to bottom, or as directed)
3. Read the relevant SPEC.md / UX.md / BUILD.md section
4. Do the work
5. Update the task in `phase-N.md`: mark `[ ]` → `[x]`, add a note
6. If the task created new files, note what was created
7. If all tasks in a phase are done: update `TASKS/PHASES.md`, mark phase complete, move to next

## If Something Changes / Needs Update

- **Decision changed?** → Update SPEC.md first, then update TASKS files if affected
- **New research needed?** → Add to `PLAN/research/` with a note
- **Feature scope changed?** → Note in the task file, flag in the commit
- **Blocked on a question?** → Note the blocker in the task, ask Dennis

## Output Standards
- Tag findings: [CONFIRMED], [UNCONFIRMED], [SPECULATION], [REJECTED]
- Commit messages: `type(scope): description` (e.g., `feat(phase-1): add StoryCard component`)
- Never leave a phase with uncommitted changes

## If Stuck
Ask Dennis. Do not guess at Nostr protocol behavior.
