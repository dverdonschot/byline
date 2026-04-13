# Next 10 Questions — Byline Phase 2

These emerge from the foundational research. Once answered, we can write SPEC.md and start building.

---

## NQ1: What is the tech stack decision?

We have 4 options now. Dennis flagged Vite + Deno as interesting.

| | Option A | Option B (rec) | Option C | Option D (NEW) |
|-|----------|----------------|----------|----------------|
| Frontend | SvelteKit | Next.js 14 | Bun + Hono | Vite + React/Solid |
| Backend | SvelteKit SSR | Next.js API | Bun server | Deno |
| Nostr lib | nostr-tools | NDK | nostr-tools | deno-nostr |
| Database | SQLite | Postgres | SQLite/Turso | Deno KV or Postgres |
| Deployment | VPS/Pi | Vercel/VPS | Edge/VPS | Deno Deploy/VPS |
| Strength | Lightweight | SEO+ecosystem | Fastest edge | Modern, secure |

Dennis, tell me more about what draws you to Vite + Deno? I can research this specific combo if you want.

---

## NQ2: What is the dedicated Byline tag?

The discovery tag that marks all Byline content. Options:
- `byline` — clean, obvious, easy to search
- `longform` — broader, not Byline-specific
- `blog` — generic, competitive
- `nostr-blog` — descriptive but long

Also: do we use namespaced sub-tags? (`byline/dennis`, `byline/cooking`) or flat? (`dennis`, `cooking`)

**STATUS:** [OPEN] — Dennis wants a task to create a name. Generating logo concepts and name options now.

---

## NQ3: What is the content policy?

**STATUS:** [CONFIRMED] — Nostr-level free speech for now. No restrictions beyond what Nostr already does.

---

## NQ4: How does the featured page curation work?

**STATUS:** [OPEN] — Decide later.

---

## NQ5: What does the write/edit/delete UX look like?

**STATUS:** [CONFIRMED]
- Latest version shown only (NIP-23 creates new event on edit, Byline filters to latest)
- Import existing posts: paste markdown or import from URL
- Delete: yes, if Nostr supports it (NIP-23 delete creates a delete event kind 5)

---

## NQ6: How does the Daybook teen-variant work?

**STATUS:** [PARTIAL] — Self-hosted Docker configuration is important. Age verification, guardian visibility, and other specifics deferred.

---

## NQ7: What is the MVP feature set?

**STATUS:** [OPEN] — Needs Dennis input.
- Is the MVP hosted Byline only, or self-hosted Docker too?
- Does MVP include Lightning/zaps, or is that v2?
- Does MVP include Daybook?
- What is the minimum viable discovery? (Just front page with search? A filter page?)
- Does MVP include an editor, or do users paste markdown?

Dennis said self-hosted is important for Byline to work — does this mean self-hosted Docker is MVP requirement?

---

## NQ8: How does the Alby login flow work exactly?

**STATUS:** [CONFIRMED]
- "Connect with Alby" button as primary login
- No Alby → "Create Nostr key" (in-browser) or "I have nos2x"
- Profile name/picture: from Nostr kind-0 profile, editable in Byline
- Post-login: prompt to write + front-page

---

## NQ9: What is the monetization model?

**STATUS:** [CONFIRMED] — Yes, small Byline platform split on zaps. NIP-57 multi-p tag with weight (e.g., 95% author, 5% Byline). Details deferred until revenue model is needed.

---

## NQ10: What is the brand and name?

**STATUS:** [OPEN] — Dennis wants to check names. Generating name options and logo concepts.

**Name options generated (2026-04-13):**
1. **Byline** — working title, journalist term, author-facing, clean
2. **Inkwell** — writing tool, premium, editorial feel
3. **Folio** — page/sheet of paper, classic publishing term
4. **Parchment** — old-world writing surface, warm, literary
5. **Longform** — descriptive, no-nonsense
6. **Draft** — the thing a writer produces, iterative, accessible

**Logo concepts generated:**
- Logo 1: lowercase "b" with lightning bolt in bowl — premium editorial
- Logo 2: quill tip with ink drop — classic stationery feel
- Logo 3: single pen stroke with fork — signal + pen combined

Dennis to choose from options or pick a direction.

---

## How These Questions Get Answered

Each question needs a decision. Some can be made by Dennis alone (NQ2, NQ3, NQ10). Some need research (NQ4, NQ6). Some need both.

Once these 10 are answered: write SPEC.md → start building.
