# Research: Moderation & Safety (Q8)

**Status:** [CONFIRMED — general framework clear, Daybook variant needs more decisions]

## Nostr's Native Moderation Tools

### NIP-51 (Mute Lists / Stallmanisms)
NIP-51 defines structured lists stored on Nostr as kind 30000-30099:
- **kind 30000** — Mute list (users you mute)
- **kind 30001** — Pin list (bookmarks / blogroll)
- **kind 30002** — Relay list (preferred relays)
- **kind 30003** — Bookmark list
- **kind 30004+** — Custom categorized lists

These are signed events stored on relays — any client can read them and apply the mute.

**Limitation:** Mute lists only work client-side. If content is on a relay, it's still there — the client just doesn't show it.

### Relay-Level Moderation
Relays can implement their own filtering policies:
- Some relays filter by content type (no porn, no long-form, etc.)
- NIP-51 relay list lets relays announce their policies
- Byline could run or partner with relays that moderate certain content categories

**For Byline:** We choose which relays to publish to. We can avoid relays with problematic content.

## EU DSA Compliance

The **Digital Services Act (DSA)** applies to Byline if it's deemed an "online platform" serving EU users. Key obligations:

### If Byline is a "Very Large Online Platform" (VLOP) — unlikely at startup
- Algorithmic risk assessments
- Independent audits
- Crisis response protocols

### For all online platforms serving EU users:
1. **Notice and takedown mechanism** — users must be able to report illegal content with a clear process
2. **Statement of reasons** — if Byline removes content, must explain why to the user
3. **Appeal mechanism** — internal complaints handling
4. **Trusted flagger status** — can designate trusted entities for faster content review
5. **Transparency reports** — annual reports on content moderation actions

### What this means for Byline
- **Featured page AI moderation:** If Byline uses AI to read stories before featuring them, this counts as a content moderation action — must have appeal process
- **Notice button:** Every story needs a "Report" button → formal notice → Byline reviews → potential removal from featured page (not from Nostr itself — Byline can't delete from relays)
- **Privacy:** GDPR applies — user data, Lightning payments, session data

### Practical compliance without over-burdening:
- Start with a simple report form (email or in-app)
- For featured page: clear criteria (what gets featured), right to appeal
- Keep moderation logs
- The content is on Nostr — Byline can only remove from its own featured page, not from the network

## Daybook (Teen Variant) — Safety Model

**Core principle:** Daybook content is always on Nostr (public network). Safety must come from access control, not from hiding content.

### Age-Gating
- Daybook requires **verified age** before signup
- Options: selfie + ID verification (SumSub, Onfido), or parental consent flow
- EU: COPPA-equivalent requires parental consent for under-16

### Parental/Guardian Visibility
- Guardian gets a "family view" with read access to the teen's Daybook
- Implemented via Nostr encrypted DMs (NIP-04 / NIP-44) — guardian npub added as authorized reader
- Guardian cannot delete content (teen owns their keys), only see it

### Private Sharing
- Daybook posts can be:
  - **Public** — visible to anyone with `byline/teen` tag
  - **Family-only** — encrypted to guardian npub
  - **Unlisted** — published to Nostr but with no tag (only with link)
- NIP-44 encryption is the standard for private Nostr DMs

### Content Safety for Daybook
- **No algorithmic recommendation** of Daybook content to unknown users
- Featured page for Daybook shows only AI-checked, guardian-approved content
- Reports handled within 24h by human moderator
- Under EU law, platforms with under-16 users have heightened duty of care

## Moderation Architecture for Byline

| Layer | Tool | Notes |
|-------|------|-------|
| Featured page | AI read (Byline server) | Story must pass AI check to appear on featured |
| Report mechanism | In-app report form | Submitted stories reviewed by human |
| User-level | NIP-51 mute lists | Users manage their own mute/block |
| Relay level | Choose moderate relays | Partner with relays that filter |
| Daybook | Age verification + NIP-44 encryption | Separate app or subdomain |

## Gaps to Decide (Dennis input needed)
- What is Byline's content policy? (NSFW allowed? Political content?)
- Who handles reports on Daybook?
- How to handle jurisdiction for teen users in different countries?
