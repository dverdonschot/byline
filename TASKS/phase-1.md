# Phase 1 — Read Path

**Goal:** A reader can browse the home feed, open any article, filter by tag/author, and view author profiles. All without logging in.
**Started:** ⏳
**Completed:** ⏳

## Prerequisites
- Phase 0 fully complete (all 9 sections done)

---

## Tasks

### 1.1 — Layout Components
- [ ] `NavBar` — Logo, search bar, login/write buttons, avatar dropdown; responsive (hamburger on mobile)
- [ ] `Footer` — Links: About, GitHub, Nostr, RSS; "Zaps go to authors"
- [ ] `PageLayout` — wraps NavBar + content + Footer
- [ ] Verify: all pages render NavBar and Footer consistently

### 1.2 — Home Page (`/`)
- [ ] Static version with placeholder StoryCards (no data)
- [ ] Hero section: headline + two CTA buttons ("Browse stories", "Start writing")
- [ ] Featured section (3 ArticleCards, empty if no featured articles)
- [ ] Recent section: grid of StoryCards with pagination
- [ ] Load real data once API is wired up (see 1.5)

### 1.3 — StoryCard Component
- [ ] Props: title, summary, authorName, authorAvatar, publishedAt, tags, zapCount, readTime, coverImage?
- [ ] States: default, hover (shadow lift + accent left border)
- [ ] Skeleton variant: animated placeholder while loading
- [ ] Links to `/story/:naddr`

### 1.4 — ArticleCard Component (Featured)
- [ ] Larger variant of StoryCard with cover image prominent
- [ ] Same props, different layout

### 1.5 — Nostr Relay Query (Browser-Side)
- [ ] Configure default relay list in `src/config/relays.ts` (4 relays from SPEC.md)
- [ ] Create `src/lib/relay.ts` — query relays in parallel, return first valid response
- [ ] Create `src/lib/nostr.ts` — `getArticles(filters)`, `getArticle(naddr)`, `getProfile(npub)`, `getZaps(eventId)`
- [ ] Use `nostr-tools/pure` for all queries (no NIP-07 dependency in read path)
- [ ] Verify: hardcode an naddr from a real Nostr article, render it in Article page

### 1.6 — Article Page (`/story/:naddr`)
- [ ] Route: decode naddr → fetch kind 30023 from relays via nostr-tools
- [ ] Article header: title (serif h1), author + date + read time + tags
- [ ] ZapButton (static, links to /login if not authenticated — no WebLN yet)
- [ ] Cover image if present
- [ ] Article body: MarkdownRenderer (see 1.8)
- [ ] AuthorCard at bottom
- [ ] Related articles (same tags)
- [ ] 404 state if article not found on any relay

### 1.7 — MarkdownRenderer Component
- [ ] Uses `marked` library (lightweight, fast)
- [ ] Sanitizes output (no raw HTML)
- [ ] Styled prose: headings (serif), body (17px, 1.7 line-height), code blocks, blockquotes, images
- [ ] Open external links in new tab
- [ ] Rendered at `/story/:naddr`

### 1.8 — Filter Page (`/filter`)
- [ ] Tag filter input (type + enter to add as chip)
- [ ] Author npub input
- [ ] Search input (queries title/summary)
- [ ] Active filters as removable chips
- [ ] Results count + paginated StoryCard grid
- [ ] Empty state with suggestions

### 1.9 — Author Profile Page (`/author/:npub`)
- [ ] Profile header: avatar, display name, bio, Lightning address badge
- [ ] npub with copy button
- [ ] Stats row: N stories, total zaps received
- [ ] All their stories as StoryCard grid
- [ ] Follow button (writer feature — button exists but disabled/greyed in Phase 1, full function in Phase 2)

### 1.10 — Loading + Error + Empty States
- [ ] `LoadingSkeleton` — variants: StoryCardSkeleton, ArticleSkeleton, ProfileSkeleton
- [ ] `ErrorState` — title, message, retry button
- [ ] `EmptyState` — title, message, optional CTA

### 1.11 — Pagination
- [ ] Offset-based pagination on home feed and filter results
- [ ] "Load more" button (not infinite scroll for MVP)
- [ ] URL reflects current page: `/?page=2`

### 1.12 — Backend API (Read Path)
- [ ] `GET /api/articles` — list articles from Postgres (filter: tag, author, search, limit, offset)
- [ ] `GET /api/article/:naddr` — get single article metadata
- [ ] `GET /api/profile/:npub` — get cached profile
- [ ] `GET /api/zaps/:articleId` — get zap count
- [ ] `GET /api/featured` — get featured articles
- [ ] `GET /api/relays` — return configured relay list
- [ ] Wire home page to call backend API (which reads from Postgres cache)

---

## Exit Criteria (Phase 2 Unlocks When)
- [ ] Home page shows real articles from Nostr relays
- [ ] Can click any article and read full markdown content
- [ ] Can filter by tag and see results
- [ ] Can view any author's profile with their articles
- [ ] All loading/error/empty states render correctly
- [ ] No TypeScript errors, no console errors
