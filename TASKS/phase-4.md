# Phase 4 — Polish

**Goal:** The app feels complete, refined, and ready for real users. No obvious gaps.
**Started:** ⏳
**Completed:** ⏳

## Prerequisites
- Phase 3 fully complete

---

## Tasks

### 4.1 — Import Paste
- [ ] On Write page: "Import" button
- [ ] Click → modal with large textarea: "Paste your markdown here"
- [ ] On paste/confirm: populate editor with pasted content
- [ ] Works with clipboard API (Ctrl+V detection)

### 4.2 — Cover Image UX
- [ ] URL input on Write page with live preview
- [ ] Drag-and-drop image onto editor (stores URL only — no upload server)
- [ ] Error state: invalid URL or image fails to load shows placeholder

### 4.3 — Article Metadata Display
- [ ] Article page: word count, published date (relative + absolute on hover), last updated, tags
- [ ] Read time shown: "{N} min read" based on 200 wpm

### 4.4 — Related Articles
- [ ] Bottom of Article page: "More from {tag}" — up to 3 articles with same primary tag
- [ ] Fetched from Postgres cache (already indexed by tag)

### 4.5 — Share Article
- [ ] "Share" button on article page
- [ ] Copy link to clipboard (naddr-based URL)
- [ ] "Copy naddr" — copies raw naddr string
- [ ] "Share on Nostr" — opens nostr: URI with event (for clients that support it)

### 4.6 — AuthorCard Component
- [ ] Compact inline card shown at bottom of article
- [ ] Avatar, display name, npub (truncated), bio excerpt, Lightning badge
- [ ] Links to `/author/:npub`

### 4.7 — Responsive Design
- [ ] Mobile-first CSS: breakpoints at 640px (sm), 1024px (lg)
- [ ] NavBar: hamburger menu on mobile, full nav on desktop
- [ ] Home grid: 1 column (mobile), 2 columns (tablet), 3 columns (desktop)
- [ ] Editor: full-width on mobile, split pane on desktop
- [ ] Article: max-width 680px centered on large screens (optimal reading width)

### 4.8 — Focus + Keyboard Nav
- [ ] All interactive elements have visible focus states (accent ring)
- [ ] Skip-to-content link (visually hidden until focused)
- [ ] Tab order is logical
- [ ] Escape closes modals/dropdowns

### 4.9 — SEO
- [ ] `<title>` and `<meta name="description">` per article (from summary tag)
- [ ] Open Graph tags: `og:title`, `og:description`, `og:image` (cover image)
- [ ] Twitter card tags
- [ ] Canonical URL (naddr-encoded)
- [ ] Sitemap at `/sitemap.xml` (Phase 5 can implement properly)

### 4.10 — RSS Feed
- [ ] `GET /rss.xml` — RSS 2.0 feed of recent articles
- [ ] RSS autodiscovery tag in `<head>` on all pages
- [ ] Feed includes: title, link, description, pubDate, content (full markdown)

### 4.11 — Dark Mode Toggle
- [ ] Theme toggle in NavBar (sun/moon icon)
- [ ] Persisted in `localStorage` (not system preference by default)
- [ ] Smooth transition: `transition: background-color 200ms, color 200ms`
- [ ] No flash on page load (inline script sets `data-theme` before paint)

### 4.12 — Performance
- [ ] Images: lazy loading with `loading="lazy"`
- [ ] Markdown: render on client, consider streaming for very long articles
- [ ] Relay queries: abort signal if user navigates away before response
- [ ] Bundle size: Vite build报告显示 all chunks < 200KB gzipped

---

## Exit Criteria (Phase 5 Unlocks When)
- [ ] All pages look good on a 375px wide phone and a 1440px wide monitor
- [ ] Dark mode toggle works, persists across refresh
- [ ] Share button copies correct link
- [ ] RSS feed validates at https://validator.w3.org/feed/
- [ ] SEO meta tags visible and correct when shared on Twitter/Facebook
- [ ] No console errors on any page
