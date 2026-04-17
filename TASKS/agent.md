# TASKS/agent.md — Working Guidelines

## Core Loop
1. Read `TASKS/PHASES.md` → active phase → open `phase-N.md`
2. Pick first uncompleted task
3. Read `PLAN/SPEC.md`, `PLAN/UX.md`, `PLAN/BUILD.md` (Nostr: `PLAN/research/` then official NIPs + nostr-tools docs)
4. **Test-first**: write failing test before implementation
5. Implement (Google TypeScript Style Guide)
6. Update task: mark `[x]`, note changes + date
7. Commit when done

---

## Task Format

```markdown
- [x] Task — **Done:** YYYY-MM-DD | Changes: <what, why>
- [ ] Task — **Blocked:** <reason> | **Ask:** Dennis
```

## Status
| | |
|-|---|
| ⏳ Not started | 🔄 In progress | ✅ Done | ❌ Blocked |

---

## TDD
1. Write failing test defining expected behavior
2. Verify it fails meaningfully
3. Minimal implementation to pass
4. Refactor — tests must still pass
5. Never skip tests for speed

---

## Code Standards
- Google TypeScript Style Guide
- Strict TS: no `any`, no unsafe casts
- Nostr: implement against NIPs first (https://github.com/nostr-protocol/nips)
- No duplication, magic numbers, or premature optimization

---

## Library Policy
Before custom code, search for well-supported libs. Present to Dennis: name, version, safety, maintenance. Get approval before adding dependency.

---

## Research (Nostr Unknowns)
1. `PLAN/research/` → 2. official NIPs → 3. nostr-tools docs → 4. implement + document

---

## If Stuck
Ask Dennis. No guessing Nostr behavior, no copy-paste without understanding, no invented workarounds.

---

## Decisions Need Change
1. Propose to Dennis → 2. If approved: update `PLAN/SPEC.md` first → 3. Document rationale

---

## Commit
```
type(scope): description [Task: phase-N.N]
```
Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`
