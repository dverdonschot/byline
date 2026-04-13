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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

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

**Status:** [UNCONFIRMED] — needs research

---

## How to Use This Doc

Each question is a research thread. Run web searches, read NIP specs, test libraries, then fill in `[UNCONFIRMED]` → `[CONFIRMED]` or `[SPECULATION]` → `[CONFIRMED]` / `[REJECTED]`.

Once all 10 have answers (even if partial), use the findings to write `SPEC.md`.
