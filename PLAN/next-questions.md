# Next 10 Questions — Byline Phase 2

These emerge from the foundational research. Once answered, we can write SPEC.md and start building.

---

## NQ1: What is the tech stack decision?

**CONFIRMED: Vite + Node.js + Express ✅ (updated 2026-04-16)**

Dennis: Vite + Node.js over Deno — larger ecosystem, easier troubleshooting. Express for REST API, Drizzle for Postgres.

---

## NQ2: What is the dedicated Longform tag?

**CONFIRMED: `longform` ✅** — Discovery tag is `longform`. Sub-tags: `longform/<username>`, `longform/<topic>`.

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

**CONFIRMED: Docker self-hosted for MVP ✅** — Dennis wants to deploy his own public version. Self-hosted Docker is the primary deployment target.

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

**CONFIRMED: Longform ✅**

Dennis: "I like the name Longform. What would be a good URL that is available and fits into the nostr landscape?"
- Domain: Dennis to check availability (instantdomainsearch.com)
- Logo: Direction 3 (pen stroke + fork) ✅ — Dennis confirmed

---

## How These Questions Get Answered

Each question needs a decision. Some can be made by Dennis alone (NQ2, NQ3, NQ10). Some need research (NQ4, NQ6). Some need both.

Once these 10 are answered: write SPEC.md → start building.
