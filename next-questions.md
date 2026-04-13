# Next 10 Questions — Byline Phase 2

These emerge from the foundational research. Once answered, we can write SPEC.md and start building.

---

## NQ1: What is the tech stack decision?

We have 3 options. Dennis needs to choose.

| | Option A | Option B (recommended) | Option C |
|-|----------|------------------------|----------|
| Framework | SvelteKit | Next.js 14 | Bun + Hono |
| Nostr lib | nostr-tools | NDK | nostr-tools |
| Database | SQLite | Postgres | SQLite/Turso |
| Deployment | VPS / Pi | Vercel / VPS | Edge / VPS |
| Strength | Lightweight, cheap | Best SEO + ecosystem | Fastest, edge-native |

Dennis, which do you prefer?

---

## NQ2: What is the dedicated Byline tag?

The discovery tag that marks all Byline content. Options:
- `byline` — clean, obvious, easy to search
- `longform` — broader, not Byline-specific
- `blog` — generic, competitive
- `nostr-blog` — descriptive but long

Also: do we use namespaced sub-tags? (`byline/dennis`, `byline/cooking`) or flat? (`dennis`, `cooking`)

---

## NQ3: What is the content policy?

For the featured page AI moderation + EU DSA compliance, we need a clear policy:
- Is NSFW / adult content allowed on Byline?
- What about political content, medical misinformation, copyrighted content?
- What triggers removal from the featured page vs the platform entirely?
- Is this a curated platform (Byline chooses what stays) or open (Nostr-level free speech)?

---

## NQ4: How does the featured page curation work?

Dennis mentioned AI reads stories for the featured page. Questions:
- What AI? (Local model? API? Human review?)
- What criteria? (Quality? Freshness? Zap count?)
- Is being on the featured page opt-in or automatic?
- Who decides what "featured" means?
- Can anyone appear on featured, or is it exclusive?

---

## NQ5: What does the write/edit/delete UX look like?

We follow NIP-23 (editing creates a new event). Questions:
- Does Byline have its own Markdown editor (built-in)?
- Or does Byline accept markdown from any source?
- If editing creates a new event, does Byline automatically show only the latest version?
- Does Byline handle deletion requests? (NIP-23 doesn't have native delete — it creates a delete event)
- Can users import their existing blog posts? (Paste markdown? Import from URL?)

---

## NQ6: How does the Daybook teen-variant work?

We know the broad strokes. Need specific decisions:
- Separate app (`daybook.so`) or same app with mode switch (`byline.com/daybook`)?
- Age verification: selfie + ID (SumSub) or parental consent email flow?
- Guardian visibility: how does the guardian connect? (Nostr npub invite? QR code?)
- Is Daybook free or paid? (If paid, who pays — guardian or teen?)
- Jurisdiction: which country's laws apply for teen users globally?

---

## NQ7: What is the MVP feature set?

We need to scope for the first release. Questions:
- Is the MVP hosted Byline only, or self-hosted Docker too?
- Does MVP include Lightning/zaps, or is that v2?
- Does MVP include Daybook?
- What is the minimum viable discovery? (Just the front page with search? A filter page?)
- Does MVP include an editor, or do users paste markdown?

---

## NQ8: How does the Alby login flow work exactly?

We know the tech. Need UX decisions:
- First visit: "Connect with Alby" button prominently shown?
- If user has no Alby: fallback to "Create with Nostr key" (in-browser) or "I have nos2x"?
- Is Alby account creation part of Byline's onboarding flow?
- After login: what does the user see? (Empty feed? Prompt to write first story?)
- Profile: where does name/picture come from? (From Nostr kind-0 profile, editable?)

---

## NQ9: What is the monetization model?

We know Lightning zaps go to authors. But what about Byline itself?
- Does Byline take a cut of zaps? (NIP-57 split possible but requires Byline's Lightning Address on the author's profile)
- Is Byline free? Subscription? One-time?
- If subscription: what triggers payment? (Publishing? Storage? Featured?)
- Are there any other revenue paths? (Premium themes? Domain-customization?)

---

## NQ10: What is the brand and name?

We have "Byline" as working title. Questions:
- Is "Byline" the final name? Does it need a domain check?
- Is there a logo concept? (We can generate one with image generation)
- What's the one-liner tagline? ("Long-form stories on Nostr"?)
- What's the visual identity direction? (Dark mode? Editorial/medium-style? Something else?)

---

## How These Questions Get Answered

Each question needs a decision. Some can be made by Dennis alone (NQ2, NQ3, NQ10). Some need research (NQ4, NQ6). Some need both.

Once these 10 are answered: write SPEC.md → start building.
