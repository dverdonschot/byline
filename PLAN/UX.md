# Longform — UX Deep Dive

**Status:** Draft — for Dennis review
**Date:** 2026-04-16

---

## Core Principle

Nostr users already have a social graph — their kind 3 contact list. Longform should **read it, not replace it**. If Alice follows Bob on Damus, she sees Bob's articles on Longform the moment she logs in. No extra onboarding step.

---

## User Types

| Type | Description | Landing |
|------|-------------|---------|
| **Visitor** | Has never used Longform | Discover + login prompt |
| **Reader** | Logged in, reading only | Personal feed + explore |
| **Writer** | Logged in + publishing | Feed + Write + Profile |

Most users are Readers who will eventually become Writers.

---

## Page Inventory (full)

| Route | Page | Auth | Description |
|-------|------|------|-------------|
| `/` | **Public Feed** | No | Always the public feed — same for everyone |
| `/following` | **Following Feed** | Yes | Personal feed from Nostr kind 3 follows |
| `/story/:naddr` | **Article** | No | Full article + zap |
| `/story/:naddr/edit` | **Edit** | Yes (author) | Edit article |
| `/filter` | **Explore** | No | Tag browse, search, author search |
| `/login` | **Login** | No | Connect Nostr identity |
| `/write` | **Write** | Yes | New article editor |
| `/settings` | **Settings** | Yes | Profile, Lightning, key management |
| `/author/:npub` | **Author Profile** | No | Public profile + story list |

| `/settings/following` | **Following** | Yes | Manage Nostr follow list |
| `/moderation` | **Mod Queue** | Yes (admin) | Reported articles |

**Routing rules:**
- `/` is always the public feed — no auth state affects it
- `/following` redirects to login if not authenticated
- Logo always links to `/`
- After login: redirect to `/following` (personal feed)

---

## Route Map

```
                        ┌─────────────┐
                        │   /login    │ ←── Click "Login" (any page)
                        └──────┬──────┘
                               │
              ┌────────────────┼────────────────┼────────────────┐
              │                │                │                │
    ┌─────────▼──────┐  ┌──────▼──────┐  ┌──────▼──────┐  ┌──────▼──────┐
    │  NIP-07 + Alby │  │ In-browser  │  │   nos2x     │  │   NIP-46     │
    │  (extension)   │  │ keygen      │  │  extension  │  │ delegation   │
    └────────┬────────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘
             │                 │                │                │
             └────────────────▼────────────────▼────────────────┘
                            │
                    Logged in as Reader
                            │
           ┌─────────────────┼─────────────────┐
           │                 │                 │
    ┌──────▼──────┐  ┌──────▼──────┐  ┌──────▼──────┐
    │  /following  │  │   /write    │  │ /settings    │
    │ (feed of     │  │  (editor)   │  │ (profile,    │
    │  followed)   │  │             │  │  following)  │
    └──────────────┘  └─────────────┘  └──────────────┘
```

---

## Visitor Flow (Unauthenticated)

Landing on `/` for the first time:

```
┌─────────────────────────────────────────────────┐
│ NavBar: Longform [Search]      [Login] [Write] │
├─────────────────────────────────────────────────┤
│                                                 │
│  Hero:                                          │
│  "Long-form stories, owned by you"              │
│  [Browse stories]  [Start writing]              │
│                                                 │
├─────────────────────────────────────────────────┤
│  Section: Featured                              │
│  [Card] [Card] [Card]                          │
├─────────────────────────────────────────────────┤
│  Section: Recent                                │
│  [StoryCard] [StoryCard] [StoryCard] ...       │
│  [Load more]                                    │
├─────────────────────────────────────────────────┤
│  [Footer]                                       │
└─────────────────────────────────────────────────┘
```

**Key observations:**
- Can read everything without logging in
- "Login" and "Write" buttons visible but muted
- Login prompt on Write: "Connect your identity to start writing"
- No paywall, no friction to read

**Article page for visitor:**
- Full article readable
- Zap button visible but shows "Login to zap" → routes to `/login`
- Author name is a link to `/author/:npub`

---

## Login Flows

### Path A: NIP-07 Extension (Alby, nos2x, etc.)

This is the **best experience**. No password, no key handling.

```
1.  Click "Login"
2.  Choose "Connect with Alby" (or "Use extension")
3.  Alby popup opens: "Longform wants to connect your Nostr identity"
    → User approves
4.  Longform reads window.nostr.getPublicKey() → npub
5.  Longform reads kind 3 contact list from relays → list of followed npubs
6.  Redirect to /following (shows feed of followed authors)
7.  If first time: welcome banner "We imported your Nostr follows"
```

**What the user sees immediately:**
- Their name/picture loaded from kind 0 profile
- Feed populated from people they already follow on Nostr
- No empty state, no "find authors to follow" required

**Edge case — no extension:**
- Show friendly message: "No Nostr extension detected. We recommend Alby."
- Link to https://getalby.com or the browser extension store
- Show fallback option: "Use a private key instead" (Path B)

### Path B: In-Browser Keygen (Private Key)

For users without an extension. They provide their **hex private key**.

```
1.  Click "Login" → "Use private key"
2.  Show warning (must be acknowledged):
    ⚠️ "Your private key never leaves your browser.
        We do not store it. If you clear this browser's
        data, your key is gone unless you have a backup."
3.  User pastes their hex nsec OR
    User clicks "Generate new keypair" (creates one for them)
4.  Key stored in localStorage
5.  Redirect to /following (same as Path A)
```

**Key generation flow:**
- Use `nostr-tools` to generate `PubKey` in browser
- Store in `localStorage` with key `longform_sk`
- Show "Download backup" button (downloads `.txt` with npub + nsec)
- Show QR code of nsec for easy backup

**Critical UX:**
- The warning must be impossible to dismiss without reading
- "Generate keypair" clearly labeled, no surprises

### Path C: NIP-46 Delegated Signing

For users who want their key on a different device (e.g., key on phone, browsing on desktop). Uses NIP-46 Nostr Wallet Connect.

```
1.  Click "Login" → "Use Nostr Connect"
2.  User pastes their nsec (from their mobile wallet) OR
    scans QR code with their Nostr signer app
3.  Longform establishes NIP-46 connection to the delegating signer
4.  Signing requests are delegated — private key never touches Longform
5.  Redirect to /following (same as Path A)
```

**NIP-46 note:** NIP-46 is the Nostr standard for delegated signing. Compatible with Nsec.app, Alby (as signer), and most mobile Nostr wallets. We implement the `connect` flow per NIP-46. Delegated signing means Longform can publish on behalf of the user without ever holding their private key.

### After Login (all paths)

User lands on `/following` — their personal feed from followed authors.
- After login: persistent banner until they download backup

### After Login (both paths)

User lands on `/following` — their personal feed from followed authors.

```
┌─────────────────────────────────────────────────┐
│ NavBar: Longform [Search] [Write] [Avatar ▼]  │
├─────────────────────────────────────────────────┤
│                                                 │
│  Welcome back, {name}                           │
│  Following (N authors)  [Manage]                │
│                                                 │
│  [StoryCard] [StoryCard] [StoryCard] ...        │
│                                                 │
├─────────────────────────────────────────────────┤
│  [Footer]                                       │
└─────────────────────────────────────────────────┘
```

**Manage → `/settings/following`** — full list of Nostr follows with unfollow.

---

## Following System

### How Following Works (Nostr NIP-02)

Nostr users follow each other via **kind 3 contact list** events. This is the standard. Every Nostr client uses it.

```
kind 3
tags: [["p", <followed-npub-1>], ["p", <followed-npub-2>], ...]
content: ""
```

**Reading follows (on login):**
- Query relays for kind 3 events from the user's npub
- Use the most recent one
- Parse the `p` tags → list of followed npubs
- Query those authors' kind 30023 articles → populate `/following` feed

**Writing follows (from Longform):**
- User clicks "Follow" on an `/author/:npub` page
- Longform reads existing kind 3, adds the new `p` tag
- Signs and publishes the updated kind 3 to relays
- The follow is now visible in Damus, Primal, every Nostr app

**Unfollowing:**
- Same process — remove the `p` tag, republish kind 3

**Following someone's Longform = following their Nostr pubkey.** No difference. No separate system.

### Discover Authors to Follow

**Explore page** (`/filter`) has a search bar:

```
┌─────────────────────────────────────────────────┐
│ [Search authors, topics, or articles...]        │
│                                                 │
│  Popular Topics                                 │
│  [#longform] [#tech] [#philosophy] [#writing]   │
│                                                 │
│  Trending Authors (most zapped this week)       │
│  [AuthorCard] [AuthorCard] [AuthorCard]         │
│                                                 │
└─────────────────────────────────────────────────┘
```

- Typing an npub and hitting enter → shows that author's profile
- Typing a topic → shows articles filtered by that tag
- Trending Authors: sorted by total zap count across all their articles (last 7 days)

**Follow from article page:**
- Bottom of every article: "More from {author}" → links to `/author/:npub`
- `/author/:npub` has a prominent "Follow" button if not already following

### Follow Button States

On `/author/:npub` page:

| State | Button | Description |
|-------|--------|-------------|
| Not following | `[Follow]` (accent) | Primary CTA |
| Following | `[Following ✓]` (muted) | Click to unfollow |
| Own profile | none | No follow button on self |

On `/following` (settings):

| Author | Status | Action |
|--------|--------|--------|
| @alice | Following | [Unfollow] |
| @bob | Following | [Unfollow] |

---

## Feed Logic

### `/following` Feed (logged in)

Shows articles from followed authors, sorted by recency.

```
Articles WHERE author_npub IN (kind 3 contact list)
ORDER BY published_at DESC
LIMIT 20
```

- If kind 3 list is empty: show empty state with "Find authors to follow" CTA
- Real-time: refresh on page focus + pull-to-refresh on mobile
- "N new articles" banner if new content found since page load

### `/` Home Feed (public)

Shows all articles tagged `longform`, sorted by recency + engagement.

```
Articles WHERE 'longform' IN tags AND is_deleted = false
ORDER BY published_at DESC, zap_count DESC
LIMIT 20
```

- No auth required
- Shows everything, not personalized
- "Featured" section: manually curated articles (Phase 5)

### `/author/:npub` Author Feed

```
Articles WHERE author_npub = :npub
ORDER BY published_at DESC
```

- Public, no auth needed
- Shows all published articles by this author
- If logged-in user follows them: shows "Following ✓" button
- Zap count aggregated across all articles shown

---

## Empty States

| Page | Empty State |
|------|-------------|
| `/following` (no kind 3) | "You haven't followed anyone yet. [Find authors on Explore →]" |
| `/following` (no articles) | "None of your followed authors have published yet. [Browse recent →]" |
| `/filter` (no results) | "No stories found for '{query}'. [Try a different topic →]" |
| `/author/:npub` (no articles) | "No stories published yet." |
| `/write` (no draft) | Blank editor with placeholder: "Tell your story..." |

---

## Write Flow (Logged-in Writer)

From `/following` or `/`:

```
[Write] button in NavBar → /write
```

```
┌─────────────────────────────────────────────────┐
│ [Draft saved 2m ago]              [Publish ▼]   │
├─────────────────────────────────────────────────┤
│ Title: [Your story title...]                    │
│ Tags: [#longform] [Add tag...]                 │
│ Summary: [One sentence summary...]              │
│ Cover: [Paste image URL...]                     │
├────────────────────┬────────────────────────────┤
│ # My Story        │ [Live preview renders here] │
│                    │                            │
│ Write markdown     │                            │
│ here...            │                            │
│                    │                            │
├────────────────────┴────────────────────────────┤
│ 342 words · 2 min read        [Preview ↔]       │
└─────────────────────────────────────────────────┘
```

**Publish flow:**
1. Click "Publish"
2. Confirmation modal: shows title, tags, summary
3. User confirms → sign kind 30023 event with NIP-07
4. Event published to 4 relays
5. Success: redirect to `/story/:naddr`
6. Error: show relay errors, allow retry

**Tag requirements:**
- `longform` tag is always included (cannot be removed)
- `longform/<username>` added automatically
- User can add custom topic tags
- Tag input: type + enter to add, click × to remove

---

## Profile & Settings

### `/settings`

```
┌─────────────────────────────────────────────────┐
│ Profile                                         │
│ [Name input]                                    │
│ [Bio textarea]                                 │
│ [Avatar URL] [preview]                         │
│ [Save]                                          │
├─────────────────────────────────────────────────┤
│ Lightning Address                               │
│ [lud16 input] — for receiving zaps             │
│ [Save]                                          │
├─────────────────────────────────────────────────┤
│ Following (N authors)      [Manage →]          │
├─────────────────────────────────────────────────┤
│ Your Public Key                                 │
│ npub1xxx...  [Copy] [View on Nostr]            │
│ Connected via: Alby / In-browser key           │
├─────────────────────────────────────────────────┤
│ Danger Zone                                     │
│ [Delete local data]                            │
└─────────────────────────────────────────────────┘
```

**"Manage →"** links to `/settings/following`.

### `/settings/following`

```
┌─────────────────────────────────────────────────┐
│ Following — manage your Nostr follows           │
│                                                 │
│ [Search your follows...]                       │
│                                                 │
│ @alice  [Following ✓] [Unfollow]              │
│ @bob    [Following ✓] [Unfollow]              │
│ @carol  [Following ✓] [Unfollow]              │
│ ...                                             │
│                                                 │
│ Showing 47 of 47 follows                       │
│                                                 │
│ Note: these are your Nostr follows. Following   │
│ someone here follows them on the Nostr network.│
└─────────────────────────────────────────────────┘
```

**Unfollow flow:**
1. Click "Unfollow"
2. Confirmation: "Unfollow @{name}? This will update your Nostr contact list."
3. Sign updated kind 3 → publish to relays
4. Author removed from `/following` feed immediately

---

## Zap UX

Zap requires login. If not logged in: "Login to zap" routes to `/login`.

Zap button on article page:

```
[⚡ Zap 100] [⚡ Zap 500] [⚡ Zap 1000] [⚡ Zap 5000]
```

- Amount presets: 100, 500, 1000, 5000 sats
- Click → Alby/WebLN popup → user approves → payment
- After success: "Zapped! ⚡" shown for 3s
- Zap count updates immediately

**If author has no Lightning address:**
- Zap button still shows
- Click → shows tooltip: "Author hasn't set up a Lightning address yet"
- No broken experience, just graceful degradation

---

## Summary of New Pages/Flows

| Addition | Description |
|----------|-------------|
| `/following` | Personal feed from Nostr follows — replaces home as logged-in landing |
| `/settings/following` | Manage Nostr contact list from within Longform |
| Path A (NIP-07) | Auto-imports existing Nostr follows — zero onboarding |
| Path B (keygen) | Warns user, generates/stores key, same post-login flow |
| Explore page | Search + trending + topic browsing |
| Author "Follow" | Writes back to Nostr kind 3 — works across all Nostr apps |
