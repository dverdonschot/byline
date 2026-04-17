# Phase 2 — Write Path

**Goal:** A writer can connect their Nostr identity and publish, edit, and delete articles.
**Started:** ⏳
**Completed:** ⏳

## Prerequisites
- Phase 1 fully complete

---

## Tasks

### 2.1 — Authentication (NIP-07)
- [ ] Detect NIP-07 extension: check `window.nostr` availability
- [ ] Detect Alby: check `window.alby` availability
- [ ] `useSession()` React hook — manages current session state
- [ ] `AuthContext` — provides session to entire app
- [ ] `AlbyConnectButton` — flow: click → `window.alby.enable()` → read npub → store session
- [ ] `NostrKeyButton` — in-browser keygen via `nostr-tools`, store in localStorage, show warning modal
- [ ] `ExtensionButton` — `window.nostr.getPublicKey()` for nos2x / other NIP-07
- [ ] On login success: import kind 3 contact list from relays, redirect to `/following`
- [ ] Session persisted in `localStorage` (not cookies — stateless JWT-free design)
- [ ] Logout: clear localStorage session, redirect to `/`

### 2.2 — Login Page (`/login`)
- [ ] Three login options as cards: Alby, Nostr Key, Extension
- [ ] Security note footer about private key handling
- [ ] If already logged in: redirect to `/following`

### 2.3 — Following Feed (`/following`)
- [ ] Visible only to logged-in users (redirect to /login if not)
- [ ] Query relays for kind 3 contact list of logged-in user
- [ ] Parse `p` tags → list of followed npubs
- [ ] Fetch kind 30023 events from those authors, sorted by recency
- [ ] Show "N new articles" banner if content changed since page load
- [ ] Pull-to-refresh on mobile
- [ ] Empty state: "Find authors to follow" → links to `/filter`

### 2.4 — Write Page (`/write`)
- [ ] Title input (large, prominent)
- [ ] Tag input — `longform` tag always present and locked; add/remove custom tags
- [ ] Summary input (optional)
- [ ] Cover image URL input + live preview
- [ ] Split pane: MarkdownEditor (left) + MarkdownPreview (right)
- [ ] Toggle: edit-only / split / preview-only
- [ ] Word count + estimated read time (200 wpm), live update
- [ ] Auto-save to `localStorage` every 30 seconds and on blur
- [ ] Publish dropdown: confirm + sign kind 30023 + push to relays

### 2.5 — MarkdownEditor Component
- [ ] Textarea with monospace font (SF Mono / Fira Code)
- [ ] Tab key inserts 2 spaces (no focus trap)
- [ ] Auto-resize height
- [ ] Line numbers gutter (CSS-based)
- [ ] Placeholder text: "Tell your story..."

### 2.6 — TagInput Component
- [ ] Type tag name + Enter to add
- [ ] Click × to remove
- [ ] `longform` tag: visually locked (greyed × button)
- [ ] Autocomplete from existing popular tags (deferred to Phase 4)

### 2.7 — Publish Flow
- [ ] Confirmation modal: shows title, tags, summary before publishing
- [ ] Sign kind 30023 event via NIP-07 (`window.nostr.signEvent()`)
- [ ] Publish to 4 relays in parallel, wait for 2 confirmations
- [ ] On success: POST to backend (write to Postgres cache), redirect to `/story/:naddr`
- [ ] On error: show relay errors, allow retry
- [ ] "published" toast on success

### 2.8 — Edit Article (`/story/:naddr/edit`)
- [ ] Only accessible to article author (check npub match)
- [ ] Pre-fill: title, tags, summary, cover image, content
- [ ] Same editor as Write page
- [ ] Publish: same kind 30023 with same `d` tag, new `created_at`
- [ ] "Updated" toast on success

### 2.9 — Delete Article
- [ ] Delete button on article page (only for author)
- [ ] Confirmation modal: "This will remove from Byline. Nostr cannot un-delete."
- [ ] Sign kind 5 event via NIP-07
- [ ] POST to backend: mark soft-deleted in Postgres, invalidate cache
- [ ] Redirect to `/following` after delete

### 2.10 — Settings Page (`/settings`)
- [ ] Profile section: display name, bio (textarea), avatar URL (with preview)
- [ ] Lightning address: lud16 input with validation
- [ ] Public key display: npub with Copy + "View on Nostr" link
- [ ] Connected via badge: Alby / Nostr Key / nos2x
- [ ] Save: sign and publish kind 0 event to relays
- [ ] "Delete local data" button: clears localStorage, redirects to `/`

### 2.11 — Settings Following (`/settings/following`)
- [ ] List all followed npubs (from kind 3)
- [ ] Show: avatar, display name, npub
- [ ] Unfollow button per author
- [ ] Unfollow: sign updated kind 3, publish to relays, update local list
- [ ] Search within follows

### 2.12 — Backend: Write APIs
- [ ] `POST /api/publish` — verify kind 30023 signature, write to Postgres, invalidate Redis cache
- [ ] `POST /api/delete` — verify kind 5 signature, soft-delete in Postgres, invalidate cache
- [ ] `PUT /api/profile` — verify kind 0 signature, update Postgres profile cache
- [ ] `POST /api/session` — validate NIP-07 signed event, create session record
- [ ] `DELETE /api/session` — destroy session
- [ ] `GET /api/me` — return current session info

---

## Exit Criteria (Phase 3 Unlocks When)
- [ ] Can log in with Alby, see existing Nostr follows imported
- [ ] Can write and publish a new kind 30023 article to Nostr relays
- [ ] Can edit and re-publish an existing article
- [ ] Can delete an article (kind 5)
- [ ] Can update profile (name, bio, avatar, Lightning address)
- [ ] Can manage follow list from within Byline
- [ ] All data persists correctly on Nostr relays (verify with another Nostr client)
