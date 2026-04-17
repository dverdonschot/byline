# 10 Foundation Questions — Byline

These questions define the project. Each answer is filled in as research progresses. The goal: enough concrete answers to write SPEC.md and start building.

---

## Q1: How does Nostr store long-form content?

**What we need to know:**
- Which NIP covers long-form blog posts? (NIP-23? Others?)
- What event `kind` number is used?
- What is the event structure (title, content, tags, published date)?
- Can markdown be stored as plain text, or does it need encoding?
- Where does metadata live (author name, profile picture, cover image)?
- Can a single post have multiple tags (topics)?
- How does editing / deletion work on Nostr for long-form?

**Why it matters:** This is the foundation. If Nostr already has a well-defined NIP for long-form content, our job is mostly integration. If not, we need to design our own structure and accept reduced interoperability.

**Status:** [CONFIRMED — NIP-23]

**Dennis says:**
- Follow Nostr protocol, don't fight it
- Markdown is a suggestion. We need a comprehensive event structure following Nostr best practices dedicated to longform stories
- Publishing and maintaining stories are two separate concerns — Byline handles display/maintenance, not the writing tool

**Answer:** NIP-23 defines **kind 30023** as the standard long-form article event. Structure:
- `kind: 30023` (drafts use kind 30024)
- `content`: plain Markdown text (no HTML)
- `d` tag: article slug/identifier (required for editing)
- `title`, `published_at`, `t` (topics), `image`, `summary` as standardized tags
- Editing creates a new event with updated `created_at` — use `d` tag to track latest version
- Replies use kind 1111 (NIP-22)
- Articles are addressable via NIP-19 `naddr` code
- Publishing and maintaining are two separate concerns — Byline handles display/maintenance, not the writing tool

---

## Q2: Which relays should we use, and should we run our own?

**What we need to know:**
- What is the current relay landscape for long-form content? Which relays accept kind 30023?
- What are the practical relay operators (e.g., relay.damus.io, nostr.land, spiralcow)?
- What storage policies do relays have (size limits, retention)?
- Should we run our own relay? What software (nostr-rs-relay, Iris, others)?
- Can we use a small set of public relays reliably, or do we need redundancy?
- What are the costs (compute, bandwidth, storage)?
- Do relays support tag-based filtering efficiently, or do they return full datasets?

**Why it matters:** Byline doesn't need to reinvent publishing, but it does need reliable storage. Relay choice affects censorship resistance, uptime, and performance.

**Status:** [CONFIRMED]

**Dennis says:**
- Public relays first
- Own relay is mid-term future consideration

**Answer:** Use 4-6 well-maintained public relays: `relay.damus.io`, `purplepag.es`, `nos.lol`, `relay.nostr.bg`. Most public relays support kind 30023. No own relay initially. Medium-term: nostr-rs-relay (Rust, single binary) or Shugur Relay (production-ready, built on nostr-rs-relay) for a Byline-dedicated relay with NIP-51 filtering. Storage limit awareness: keep article content under ~50KB, host images externally via NIP-94.

---

## Q3: How does Lightning Network payment to authors work?

**What we need to know:**
- How do zaps (NIP-57) technically work? What is the flow (reader → author)?
- What does an author need to receive zaps? (Lightning Address? LNURL? Zap pool?)
- Can a nostr profile have a Lightning Address attached to it? How?
- What tools/libraries handle zap receipt without running a full Lightning node?
- Can we avoid a full node? (e.g., using a service like Alby, or a zap pool service)
- What fee models exist? Does every relay or tool take a cut?
- Can zaps be split (e.g., 90% author, 10% Byline platform)? How?
- What is the reader experience? QR code? Browser extension? Nostr wallet connect (NWC)?

**Why it matters:** Lightning integration is a core value proposition. It needs to be frictionless for both readers and authors.

**Status:** [CONFIRMED — NIP-57]

**Dennis says:**
- Look at Primal for how they implement Lightning/zaps
- Research how Primal does it — they have a working production implementation

**Answer:** NIP-57 defines zap flow: kind 9734 (zap request, not published) → HTTP GET to author's lnurl server → invoice generated → paid → kind 9735 (zap receipt, published to relays). Primal uses **WebLN + Alby** for one-tap zaps — reader has Alby extension, Primal calls `webln.enable()` to get invoice, Alby pays. Author needs only a **Lightning Address** (lud16 in their Nostr profile). We don't need a full Lightning node — Alby (or any lnurl-pay provider) handles receipt generation. Zap splits (NIP-57) technically possible via multi-p tags with weights. No platform cut initially.

---

## Q4: How does a user create and manage their Nostr identity in Byline?

**What we need to know:**
- How does a new user get a Nostr keypair? (Generate in-browser? BDK/wallet integration?)
- What is the UX for key management? (Raw nsec is dangerous — what alternatives exist?)
- What is Nostr Wallet Connect (NWC)? How does it compare to raw key storage?
- Can users sign in with a browser extension (nos2x, Alby)? Does Byline support that natively?
- How does multi-device work? (User has phone + desktop — how does both sign?)
- Should Byline support delegated signing (signing on behalf of user, like a server-side approach)?
- What about npub-based profiles — can we fetch profile metadata (name, picture, bio) from relays?

**Why it matters:** Identity is the hardest UX problem in Nostr. If key management is painful, users leave. We need a balance between self-sovereignty and usability.

**Status:** [CONFIRMED — well-specified by Nostr]

**Dennis says:**
- Initially assume user already has a Nostr account
- If easy enough, allow creating a new Nostr account within Byline
- Follow Nostr and Lightning best practices

**Answer:** Byline uses **NIP-07 browser extension signing** — we never touch private keys. Supported extensions: **Alby** (most feature-rich, has Lightning built-in) and **nos2x** (minimalist). If user has no Nostr account, nostr-tools can generate a keypair in-browser (store in localStorage as fallback, warn user to back it up). Profile metadata (name, picture, bio) fetched from kind-0 events on relays. Multi-device: NIP-46 (delegated signing) allows server-side signing without key exposure — defer for v1.

---

## Q5: What does the frontend architecture look like?

**What we need to know:**
- SPA (Single Page App) vs SSR (Server-Side Rendered) — what are the tradeoffs for Nostr?
- Can we query multiple relays simultaneously from the browser? How?
- What Nostr JS libraries exist? (nostr-tools, nostr-hooks, others)
- Is there a good React/Vue/Svelte integration pattern?
- For a blog reader: how do we fetch all posts for a given tag efficiently?
- How do we handle images? (Host on nostr? External URL? What about image hosting?)
- How do we render markdown stored in Nostr events? (use a markdown parser client-side?)
- Should Byline be a progressive web app (PWA)? Mobile-first?

**Why it matters:** The reader and writer experience defines Byline's success. It needs to feel fast and polished, not like a rough Nostr demo.

**Status:** [CONFIRMED — 3 options researched, recommendation provided]

**Dennis says:**
- Markdown is not mandated — pick the tools that fit our use case best
- Use common libraries where possible to avoid creating our own mess
- If someone already created a structured library for this function, use it
- Research tech stack thoroughly

**Answer:** See `research/tech-stack-options.md` for full analysis. 3 options researched:
- **Option A (SvelteKit):** Lightweight, fast, cheap hosting, Raspberry Pi viable. nostr-tools + SQLite.
- **Option B (Next.js + NDK):** Best for SEO (SSR), largest ecosystem, NDK handles multi-relay complexity. **Recommended by Clawd.**
- **Option C (Bun + Hono):** Edge-first, fastest, Turso SQLite for edge deployment.

Images: external URLs via NIP-94. Markdown: marked.js (fast) or unified/remark (full AST control). NIP-07 signing via browser extension (Alby/nos2x) — Byline never stores keys.

---

## Q6: How does "one tag = one blog" work technically?

**What we need to know:**
- In NIP-23, tags are just `["t", "tagname"]` in the event tags — is that sufficient for blog identification?
- Can a single tag namespace be "owned" by one author, or is any tag open?
- How do we prevent tag collision (two different "cooking" blogs)?
- Should Byline blogs use namespaced tags (e.g., `byline_dennis`, `byline_cooking`)?
- Can we support sub-tags (e.g., `cooking/pasta`, `cooking/baking`)?
- Can a blog have multiple tags (one author, many topics)?
- How do we handle tag discovery — do readers follow a tag like RSS?

**Why it matters:** This is the core product architecture. It determines whether Byline is a network of blogs or a single blog platform.

**Status:** [CONFIRMED]

**Dennis says:**
- Work within the context of Nostr tags
- Show all longform content but allow filtering on different things
- Byline website should have a dedicated tag that is featured more quickly
- A front page with featured stories, and a filter page where you can find users and specific tags/subjects

**Answer:** NIP-23 `t` tags are the blog identifier — use multiple `t` tags per article for multi-topic. Tag collision is a known Nostr issue — Byline uses a **dedicated discovery tag** (e.g., `byline`) so all Byline content is filterable. Individual blogs use sub-tags (`byline/<username>`, `byline/cooking`). Front page shows all content with `byline` tag (or featured/liked). Filter page queries relays for all kind 30023 events with `t=byline` and optionally `t=byline/<topic>`.

---

## Q7: How do readers discover content and subscribe to blogs?

**What we need to know:**
- How do readers find Byline blogs without knowing the Nostr npub? (Vanity URLs? Search?)
- Can we build a "relay of tags" — a discovery layer that indexes posts by tag?
- Should Byline have its own relay that only stores Byline-tagged content?
- How does following work in Nostr? (Kind 3 contacts list — can Byline use that?)
- Can we build a "blogroll" (list of followed blogs) stored in a user's Nostr profile?
- How does SEO work for Nostr blogs? (Public content = can Google index it?)
- What is the equivalent of RSS for Nostr tags?

**Why it matters:** Nostr's discovery UX is raw. If Byline doesn't solve discovery, it won't grow beyond early adopters.

**Status:** [CONFIRMED — general architecture clear]

**Dennis says:**
- Front page with featured stories
- Filter page to find users and specific tags/subjects

**Answer:** Discovery via Byline's own relay/indexer that subscribes to kind 30023 events with the `byline` tag. Front page: featured stories (recent + liked/zapped count). Filter page: browse by tag, search by npub or topic. Following: users follow npubs (kind 3 contact list) stored in their Nostr profile. Blogroll stored in kind 30001 (bookmark list) as NIP-51. SEO: public content is crawlable — Next.js SSR + meta tags = good indexing. RSS equivalent: NIP-03 (signed JSON WS feed) for relay subscriptions.

---

## Q8: How does moderation work? What is the safety model?

**What we need to know:**
- What moderation tools does Nostr natively provide? (Mute lists, block lists — NIP-51?)
- Can relays filter content? Can Byline choose relays that moderate?
- How do we handle the teen-variant (Daybook)? What age-gating is needed?
- What data does a parent/guardian have visibility over in Daybook?
- How do we prevent Byline from becoming a vector for harmful content in the teen variant?
- Is NIP-51 (Stallmanisms / tag moderation) sufficient, or do we need more?
- What is the DM / private sharing model? (Can a teen blog be private to family only?)

**Why it matters:** The teen variant (Daybook) has real safety implications. We need a clear model before building anything.

**Status:** [CONFIRMED — framework clear, Daybook decisions needed]

**Dennis says:**
- For the featured page, AI reads the stories for moderation
- Not every searchable thing needs to be scanned — focus on featured content
- Research EU law compliance (DSA, GDPR, age-appropriate design) without over-burdening the platform

**Answer:** Nostr has NIP-51 mute/block lists (kind 30000+), client-side only. Relays can filter too. For Byline's featured page: AI reads stories before featuring, reports handled via in-app form. EU DSA: notice-and-takedown mechanism needed (even a simple email form counts for small platforms), statement of reasons if content removed, appeal path. GDPR: applies to user data/payments. Daybook: age verification + NIP-44 encryption for family-only posts. Guardian gets family-view via authorized npub. Key gaps to decide: content policy (NSFW?), who handles Daybook reports, jurisdiction. See `research/moderation-safety.md`.

---

## Q9: What tools and libraries should we use for the core stack?

**What we need to know:**
- **Backend:** Node.js? Deno? Bun? — which fits best for a Nostr relay / API layer?
- **Frontend:** Next.js? SvelteKit? Plain React? — which has the best Nostr library support?
- **Markdown:** What markdown parser? (marked? remark? unified?) — any that handle the specific Nostr content format well?
- **Lightning:** What Lightning library works best server-side? (LCLN ||, Rusty s新型?) — or should we use a managed service?
- **Signing:** What browser signing library? (nostr-tools, @nostr-dev/nostr-tools, nos2x?)
- **Database:** Do we need one? (For caching relay data? For blog metadata?) SQLite? Postgres?
- **Deployment:** Docker? What does the minimal deployment look like?

**Why it matters:** We need concrete tool choices before writing SPEC.md. The Nostr ecosystem moves fast — we need currently-maintained libraries.

**Status:** [CONFIRMED — 3 options, recommendation ready]

**Dennis says:**
- Research 3 big tech stack options and let Dennis choose
- I (Clawd) can pick my favorite to present as the recommended option
- Use common, well-maintained libraries

**Recommendation:** **Option B — Next.js 14 + NDK + Postgres**. Best SEO (SSR), NDK handles multi-relay complexity including outbox model, React ecosystem has everything we need, Alby/WebLN integrates cleanly. Dennis's existing ZwermAI project already uses Next.js (familiarity). See `research/tech-stack-options.md` for full comparison. Alternative: SvelteKit (Option A) if lightweight/cheap is preferred over SEO.

---

## Q10: What does the developer experience look like to launch a new Byline blog?

**What we need to know:**
- What does a developer need to do to spin up a new Byline blog instance?
- Is it one command? One Docker image? A full VPS setup?
- Can non-technical writers use Byline, or is it developer-only at first?
- What are the ongoing maintenance requirements? (Relay costs, Lightning node management?)
- Can Byline run on a Raspberry Pi? (For cheap self-hosting?)
- What is the upgrade path as the project evolves?
- How do Byline instances federate? (Can a post on one Byline instance appear on another?)

**Why it matters:** If launching a Byline blog is too complex, the network effect won't happen. We need the bar to be low.

**Status:** [CONFIRMED]

**Dennis says:** Docker spin-up with configurable tags. Hosted version on VPS with Postgres for state Nostr can't store. Alby for automatic login with Nostr. Users login to website, browse front-page/search, and create stories via Byline or link external blog stories to Nostr via Byline.

**Answer:** Docker-compose for self-hosters (Byline app + nostr-rs-relay + Postgres + Nginx). .env sets tag namespace (e.g., `byline_cooking` instead of `byline`). Non-technical writers use Byline as a website (like WordPress) — no dev skills needed. Alby NIP-07 (`window.nostr`) gives seamless login + Lightning in one popup. VPS: 2GB RAM minimum, ~€5-10/month. Instances federate automatically since all publish to the same public relays. Reference implementations: Habla News and YakiHonne. Postgres stores: sessions, article cache, moderation queue, zap totals. See `research/developer-experience.md` and `research/alby-integration.md`.

---

## How to Use This Doc

Each question is a research thread. Run web searches, read NIP specs, test libraries, then fill in `[UNCONFIRMED]` → `[CONFIRMED]` or `[SPECULATION]` → `[CONFIRMED]` / `[REJECTED]`.

Once all 10 have answers (even if partial), use the findings to write `SPEC.md`.
