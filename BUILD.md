# Longform — Complete Build Specification

**Version:** 0.4-draft
**Last updated:** 2026-04-13
**Status:** Planning — frontend architecture confirmed (direct browser relay queries)

---

## Design Language

### Colors

```css
:root {
  /* Backgrounds */
  --color-bg:           #faf9f7;   /* warm off-white, main background */
  --color-surface:      #ffffff;    /* cards, modals */
  --color-surface-2:    #f5f3f0;   /* input backgrounds, code blocks */

  /* Borders */
  --color-border:       #e8e4df;    /* subtle warm gray borders */
  --color-border-dark:  #d4cfc9;    /* stronger borders, dividers */

  /* Text */
  --color-text:         #1a1a1a;   /* primary text */
  --color-text-muted:   #6b6b6b;   /* secondary text, metadata */
  --color-text-faint:   #9a9a9a;    /* placeholders, timestamps */

  /* Accent — warm amber (Lightning color) */
  --color-accent:       #d4a574;   /* primary accent, links, CTA buttons */
  --color-accent-dark:  #b8956a;   /* hover state */
  --color-accent-light: #ede0d4;   /* accent backgrounds, tag chips */

  /* Semantic */
  --color-success:      #4a9e6b;   /* published, connected */
  --color-error:        #c45c5c;   /* errors, delete */
  --color-warning:       #c49a4a;   /* warnings */
}
```

### Typography

```css
:root {
  /* Font families */
  --font-serif:    Georgia, 'Times New Roman', serif;     /* article body, headings */
  --font-sans:     -apple-system, BlinkMacSystemFont,    /* UI, buttons, nav */
                   'Segoe UI', system-ui, sans-serif;
  --font-mono:     'SF Mono', 'Fira Code', Consolas, monospace;  /* code */

  /* Type scale */
  --text-xs:    0.75rem;     /* 12px — labels, badges */
  --text-sm:    0.875rem;    /* 14px — metadata, timestamps */
  --text-base:  1.0625rem;   /* 17px — body text */
  --text-lg:    1.125rem;    /* 18px — article body */
  --text-xl:    1.25rem;     /* 20px — subheadings */
  --text-2xl:   1.5rem;      /* 24px — article titles in lists */
  --text-3xl:   2rem;        /* 32px — page headings */
  --text-4xl:   2.75rem;     /* 44px — hero text */

  /* Line heights */
  --leading-tight:  1.3;     /* headings */
  --leading-normal: 1.7;      /* body text (generous for long-form) */
  --leading-ui:     1.4;     /* buttons, labels */
}
```

### Spacing System

```css
:root {
  --space-1:  4px;
  --space-2:  8px;
  --space-3:  12px;
  --space-4:  16px;
  --space-5:  20px;
  --space-6:  24px;
  --space-8:  32px;
  --space-10: 40px;
  --space-12: 48px;
  --space-16: 64px;
  --space-20: 80px;
  --space-24: 96px;
}
```

### Motion

```css
:root {
  --ease-out: cubic-bezier(0.16, 1, 0.3, 1);   /* smooth deceleration */
  --ease-in:  cubic-bezier(0.7, 0, 0.84, 0);  /* smooth acceleration */

  --duration-fast:   120ms;   /* micro-interactions: hover, press */
  --duration-normal: 240ms;   /* panels, modals, expanding */
  --duration-slow:   400ms;   /* page transitions */
}
```

### Border Radius

```css
:root {
  --radius-sm:  4px;
  --radius-md:  8px;
  --radius-lg:  12px;
  --radius-xl:  16px;
  --radius-full: 9999px;      /* pills, avatars */
}
```

### Shadows

```css
:root {
  --shadow-sm:  0 1px 2px rgba(0,0,0,0.05);
  --shadow-md:  0 4px 12px rgba(0,0,0,0.08);
  --shadow-lg:  0 8px 24px rgba(0,0,0,0.12);
  --shadow-xl:  0 16px 48px rgba(0,0,0,0.16);
}
```

---

## Feature Inventory

Every feature the platform needs. Divided into Reader, Writer, and Platform.

### Reader Features

| ID | Feature | Description | Priority |
|----|---------|-------------|----------|
| R1 | **Read article** | Render kind 30023 markdown as beautiful article | Must |
| R2 | **Browse home** | Public feed: all `longform` articles sorted by recency + engagement | Must |
| R3 | **Filter by tag** | See all stories under a specific tag | Must |
| R4 | **Filter by author** | See all stories by a specific npub | Must |
| R5 | **Search** | Full-text search across article titles and content | Must |
| R6 | **Author profile** | Name, bio, avatar, Lightning address, story list | Must |
| R7 | **Zap article** | Send Lightning zap to author from article page (login required) | Must |
| R8 | **Zap count** | Show total sats received per article | Must |
| R9 | **Zap count (author)** | Show total sats received across all articles | Should |
| R10 | **Article metadata** | Show published date, last updated, word count, read time | Must |
| R11 | **Article tags** | Show and link all tags on an article | Must |
| R12 | **Related articles** | Show other articles with same tag on article page | Should |
| R13 | **Share article** | Copy link, copy naddr, share via Nostr | Should |
| R14 | **Article replies** | Show kind 1111 replies to an article | Could |
| R15 | **Dark mode** | System-following dark mode (optional, post-MVP) | Could |
| R16 | **Following feed** | Personal feed from authors in Nostr kind 3 contact list | Must |
| R17 | **Follow author** | Follow button on author profile → publishes to kind 3 | Must |
| R18 | **Unfollow author** | Remove from kind 3 contact list | Must |
| R19 | **Manage follows** | View all Nostr follows, search, bulk unfollow | Must |
| R20 | **Import follows on login** | On NIP-07 login: auto-import existing kind 3 list | Must |
| R21 | **Trending authors** | Authors sorted by total zap count (last 7 days) | Should |
| R22 | **Zap login gate** | Zap button shows "Login to zap" if not authenticated | Must |

### Writer Features

| ID | Feature | Description | Priority |
|----|---------|-------------|----------|
| W1 | **Login with Alby** | NIP-07 via Alby extension — instant login | Must |
| W2 | **Login with Nostr key** | In-browser keygen + localStorage, with backup warning | Must |
| W3 | **Fallback: nos2x** | If no Alby, use nos2x extension NIP-07 | Must |
| W4 | **Write story** | Markdown editor with live preview toggle | Must |
| W5 | **Add title** | Set article title (becomes `title` tag in event) | Must |
| W6 | **Add tags** | Add/remove `t` tags (topic labels) | Must |
| W7 | **Add summary** | Set article summary (becomes `summary` tag) | Should |
| W8 | **Add cover image** | Paste URL or upload → stored as `image` tag | Should |
| W9 | **Draft save** | Auto-save draft to localStorage while writing | Must |
| W10 | **Publish** | Sign kind 30023 event, push to relays | Must |
| W11 | **Edit story** | Update kind 30023 (same `d` tag, new `created_at`) | Must |
| W12 | **Delete story** | Send kind 5 delete event | Must |
| W13 | **Unpublish** | Delete + remove from public feeds | Must |
| W14 | **Import story** | Paste markdown content → create kind 30023 | Must |
| W15 | **Profile editor** | Edit display name, bio, avatar URL | Must |
| W16 | **Lightning address** | Set lud16 field for zaps in profile | Must |
| W17 | **Preview mode** | Toggle between editor and rendered preview | Must |
| W18 | **Word count / read time** | Live word count and estimated read time while writing | Should |
| W19 | **Schedule publish** | Set future publish date (publish then) | Could |
| W20 | **Series / drafts** | Manage multiple drafts, publish one at a time | Could |

### Platform / Admin Features

| ID | Feature | Description | Priority |
|----|---------|-------------|----------|
| P1 | **Article cache** | Postgres stores latest version of articles for fast reads | Must |
| P2 | **Cache invalidation** | When kind 30023 updates on relay, refresh Postgres | Must |
| P3 | **Cache warming** | Backend polls relays for new `longform` tagged content | Must |
| P4 | **Featured page** | Curated selection shown on home page | Should |
| P5 | **AI moderation** | AI reads article content before featuring | Should |
| P6 | **Report article** | Reader submits report → stored in mod queue | Should |
| P7 | **Mod queue** | Admin view of reported articles, approve/reject | Should |
| P8 | **Zap split** | Platform takes % cut of zaps (NIP-57 multi-p) | Could |
| P9 | **Email digest** | Weekly digest of featured stories to subscribers | Could |
| P10 | **Webmention** | Receive webmentions for articles (NIP-27) | Could |
| P11 | **RSS feed** | RSS/Atom feed of featured articles | Should |

---

## Pages and Routes

### Route Map

| Route | Page | Auth | Description |
|-------|------|------|-------------|
| `/` | **Home** | No | Public feed: featured + recent |
| `/story/:naddr` | **Article** | No | Full article view |
| `/story/:naddr/edit` | **Edit** | Yes (author) | Edit article |
| `/filter` | **Explore** | No | Tag browse, search, trending authors |
| `/author/:npub` | **Author Profile** | No | Profile + stories + follow button |
| `/login` | **Login** | No | Connect Nostr identity |
| `/following` | **Following Feed** | Yes | Personal feed from followed authors |
| `/write` | **Write** | Yes | New article editor |
| `/settings` | **Settings** | Yes | Profile, Lightning, key management |
| `/settings/following` | **Following** | Yes | Manage Nostr follow list |
| `/moderation` | **Mod Queue** | Yes (admin) | Reported articles |
| `/sitemap.xml` | **Sitemap** | No | SEO sitemap |

### Page Specifications

---

#### Home (`/`)

**Purpose:** Entry point. Show best content, invite readers and writers.

**Layout:**
```
┌──────────────────────────────────────────────┐
│ NavBar: Logo | Search | Login/Get Started   │
├──────────────────────────────────────────────┤
│ Hero: "Long-form stories, owned by you"     │
│       [Browse stories] [Start writing]       │
├──────────────────────────────────────────────┤
│ Section: Featured (if featured page exists)   │
│  [FeaturedCard] [FeaturedCard] [FeaturedCard]│
├──────────────────────────────────────────────┤
│ Section: Recent                               │
│  [StoryCard] [StoryCard] [StoryCard] ...    │
│  Pagination / Infinite scroll                 │
├──────────────────────────────────────────────┤
│ Footer: About | GitHub | Nostr | Zaps go... │
└──────────────────────────────────────────────┘
```

**Components on page:** NavBar, HeroSection, FeaturedSection, StoryCard × N, Footer
**Data:** fetches recent kind 30023 events tagged `longform` from Postgres cache (or relays if cache empty)

---

#### Article (`/story/:naddr`)

**Purpose:** Read a single story.

**Layout:**
```
┌──────────────────────────────────────────────┐
│ NavBar                                      │
├──────────────────────────────────────────────┤
│ Article Header:                              │
│  Title (h1, serif)                          │
│  By [AuthorName] · [date] · [read time] · [tags]│
│  [ZapButton: ⚡ 420 sats]                    │
├──────────────────────────────────────────────┤
│ Cover Image (if present)                      │
├──────────────────────────────────────────────┤
│ Article Body:                                │
│  Rendered Markdown (serif, 18px, 1.7lh)     │
│  Code blocks, blockquotes, images, etc.     │
├──────────────────────────────────────────────┤
│ Zap Section:                                  │
│  [ZapButton: ⚡ Zap the author]              │
│  "X sats from Y zappers"                    │
├──────────────────────────────────────────────┤
│ Author Card:                                  │
│  Avatar · Name · npub · bio · Lightning addr │
│  [View all stories by this author]           │
├──────────────────────────────────────────────┤
│ Related Articles (same tags)                 │
├──────────────────────────────────────────────┤
│ Footer                                       │
└──────────────────────────────────────────────┘
```

**Components on page:** NavBar, ArticleHeader, ArticleBody, MarkdownRenderer, ZapButton, ZapSection, AuthorCard, RelatedArticles, Footer
**Data:** decodes naddr → queries relay → fetches kind 30023 event → renders markdown

---

#### Filter / Browse (`/filter`)

**Purpose:** Find stories by tag, author, or search term.

**Layout:**
```
┌──────────────────────────────────────────────┐
│ NavBar                                      │
├──────────────────────────────────────────────┤
│ Filter Bar:                                  │
│  [Tag input] [Author npub input] [Search]   │
│  Active filters shown as removable chips      │
├──────────────────────────────────────────────┤
│ Results: X stories found                     │
│  [StoryCard] [StoryCard] ...                 │
│  Pagination                                  │
└──────────────────────────────────────────────┘
```

**Components on page:** NavBar, FilterBar, TagInput, AuthorInput, SearchInput, FilterChip × N, StoryCard × N, Pagination

---

#### Author Profile (`/author/:npub`)

**Purpose:** See an author's identity and all their stories.

**Layout:**
```
┌──────────────────────────────────────────────┐
│ NavBar                                      │
├──────────────────────────────────────────────┤
│ Profile Header:                              │
│  [Avatar] · [Display Name]                  │
│  npub (truncated, copy button)               │
│  [Lightning address] ⚡                       │
│  Bio (markdown rendered, limited)            │
├──────────────────────────────────────────────┤
│ Stats: [N stories] [Total zaps received ⚡] │
├──────────────────────────────────────────────┤
│ Their Stories:                               │
│  [StoryCard] × all their stories            │
└──────────────────────────────────────────────┘
```

**Components on page:** NavBar, ProfileHeader, Avatar, LightningBadge, BioExcerpt, StatsRow, StoryCard × N

---

#### Login (`/login`)

**Purpose:** Connect a Nostr identity.

**Layout:**
```
┌──────────────────────────────────────────────┐
│ NavBar                                      │
├──────────────────────────────────────────────┤
│ Login Options (centered card):               │
│                                              │
│  [🔌 Connect with Alby]                    │
│     "Best experience — includes Lightning"   │
│                                              │
│  [Nostr Key]                               │
│     "Generate a new keypair in browser"    │
│                                              │
│  [Extension]                                │
│     "Use nos2x or other NIP-07 extension"  │
│                                              │
│  Footer note about key security              │
└──────────────────────────────────────────────┘
```

**Components on page:** NavBar, LoginCard, AlbyConnectButton, NostrKeyButton, ExtensionButton, SecurityNote

---

#### Write (`/write`)

**Purpose:** Write and publish a new story.

**Layout:**
```
┌──────────────────────────────────────────────┐
│ NavBar [Draft saved 2m ago] [Publish ▼]     │
├──────────────────────────────────────────────┤
│ Title: [large text input, placeholder...]   │
├──────────────────────────────────────────────┤
│ Tags: [tag input] [+ Add tag]               │
│        [#longform] #topic [x]               │
├──────────────────────────────────────────────┤
│ Summary: [text input, optional]             │
├──────────────────────────────────────────────┤
│ Cover image URL: [input] [Preview]           │
├──────────────────────────────────────────────┤
│ ┌─────────────────┬──────────────────┐       │
│ │ Markdown Editor │ Live Preview     │       │
│ │                 │                  │       │
│ │ # My Story     │ My Story        │       │
│ │                 │                  │       │
│ │ Write here...  │ Rendered here... │       │
│ │                 │                  │       │
│ │                 │                  │       │
│ └─────────────────┴──────────────────┘       │
│ [Word count: 0] [Read time: 0 min]         │
└──────────────────────────────────────────────┘
```

**Components on page:** NavBar, TitleInput, TagInput, SummaryInput, CoverImageInput, SplitPane (editor + preview), MarkdownEditor, MarkdownPreview, WordCount, PublishDropdown

---

#### Edit (`/story/:naddr/edit`)

**Purpose:** Edit an existing story. Same layout as Write, but pre-filled.

---

#### Settings (`/settings`)

**Purpose:** Manage account, profile, and Lightning setup.

**Layout:**
```
┌──────────────────────────────────────────────┐
│ NavBar                                      │
├──────────────────────────────────────────────┤
│ Settings Page:                               │
│                                              │
│  Profile Section:                            │
│  [Display name]                             │
│  [Bio — textarea]                           │
│  [Avatar URL] [preview]                      │
│  [Save profile]                             │
│                                              │
│  Lightning Section:                          │
│  [Lightning address] ⚡                     │
│  "Used for receiving zaps"                   │
│  [Save Lightning address]                    │
│                                              │
│  Your Public Key:                            │
│  [npub1xxx...] [Copy] [View on Nostr]      │
│                                              │
│  Connected via: Alby / Nostr Key / nos2x   │
│                                              │
│  [Delete Account Local Data]                 │
└──────────────────────────────────────────────┘
```

**Components on page:** NavBar, SettingsSection, FormInput, FormTextarea, AvatarPreview, LightningAddressInput, NpubDisplay, ConnectionBadge, DangerZone

---

## Component Inventory

Every UI component, with props and states.

### StoryCard
Displays article preview in a list.

**Props:** `title`, `summary`, `authorName`, `authorAvatar`, `publishedAt`, `tags`, `zapCount`, `readTime`, `coverImage?`
**States:** default, hover (shadow lift + accent border-left), skeleton (loading)
**Events:** click → navigate to `/story/:naddr`

### ArticleCard (variant)
Larger card for featured section.
**Same props as StoryCard but with cover image prominent.**

### ZapButton
Sends a Lightning zap to the author.

**Props:** `npub`, `naddr`, `authorLud16`
**States:**
- idle: "⚡ Zap"
- loading: spinner
- success: "⚡ Zapped!" (2s then reset)
- no-extension: "⚡ Install Alby"
- no-webln: "⚡ Get Alby"
- error: red, shows error
**Size variants:** sm (inline), md (default), lg (article page)

### ZapCount
Shows zap count on an article or author profile.

**Props:** `zapCount`, `zapperCount`
**Format:** "⚡ 420 sats from 12 zappers"

### MarkdownEditor
Textarea-based markdown editor with tab support.

**Props:** `value`, `onChange`, `placeholder`
**Features:**
- Tab key inserts 2 spaces
- Auto-resize height
- Monospace font (SF Mono / Fira Code)
- Line numbers gutter
**States:** default, focused (accent border), disabled

### MarkdownPreview
Renders markdown as styled HTML.

**Props:** `content`, `className?`
**Features:**
- Renders: headings, bold, italic, links, blockquotes, code blocks, images, lists, tables
- Sanitized (no raw HTML)
- Open external links in new tab
**States:** default, empty (shows placeholder text)

### AuthorCard
Shows author info inline on article page.

**Props:** `npub`, `displayName`, `avatar`, `bio`, `lud16`, `storyCount?`, `totalZaps?`
**Events:** click author name → `/author/:npub`

### Avatar
Circular user avatar image.

**Props:** `src?`, `npub?`, `size: sm | md | lg`
**States:**
- image loads: show image
- image fails: show generated identicon from npub
- no src and no npub: show default icon

### TagBadge
Clickable tag chip.

**Props:** `tag`, `size?: sm | md`, `onClick?: () => void`
**States:** default (accent-light bg), hover (accent-dark bg), active (pressed)
**Events:** click → navigate to `/filter?tag=tagname`

### NavBar
Top navigation bar.

**Content (not logged in):**
- Logo (left): "Longform" wordmark → links to `/`
- Search (center): search input, expands on focus
- Actions (right): [Explore] [Login]

**Content (logged in):**
- Logo (left): "Longform" wordmark → links to `/following`
- Search (center): search input, expands on focus
- Actions (right): [Explore] [Write] [Avatar ▼]
  - Avatar dropdown: [Profile] [Following] [Settings] [Disconnect]

**States:** default, scrolled (shadow appears), mobile (hamburger menu)

### Footer
Bottom of every page.

**Content:**
- Left: "Longform — Long-form stories on Nostr"
- Links: About | GitHub | Nostr | RSS
- Right: "Zaps go to authors ⚡"

### LoadingSkeleton
Animated placeholder while data loads.

**Variants:** StoryCardSkeleton, ArticleSkeleton, ProfileSkeleton

### EmptyState
Shown when a list/filter returns no results.

**Props:** `title`, `message`, `action?: { label, onClick }`

### ErrorState
Shown when a fetch fails.

**Props:** `title`, `message`, `retry?: () => void`

---

## Nostr Integration Specification

### Event Types Used

| Kind | Name | Direction | Used for |
|------|------|----------|----------|
| 0 | Profile | Read | Author display name, avatar, bio, lud16 |
| 1 | Short note | Read/Write | Replies, comments (kind 1111 preferred) |
| 5 | Deletion | Write | Delete an article |
| 30023 | Long-form | Read/Write | **Primary article storage** |
| 30024 | Draft | Read/Write | Article drafts |
| 1111 | Comment | Read/Write | Article replies/threads |
| 9734 | Zap request | Write | Reader initiates zap |
| 9735 | Zap receipt | Read | Display zap count |
| 30000-30099 | Lists | Read/Write | Mute lists, bookmark lists (NIP-51) |

### Relay Configuration

**No own relay for MVP.** Frontend queries relays directly.

**Default relay list (4 relays):**
```
wss://relay.damus.io
wss://purplepag.es
wss://nos.lol
wss://relay.nostr.bg
```

**Query strategy:**
- Read: query all 4 relays in parallel, first valid response wins
- Publish: publish to all 4 relays, wait for 2 confirmations

**Per-article relay hint:** Store the relay that first returned the event, use it as the primary relay for subsequent reads of that article.

### NIP-07 Signing Flow

```
1. Check if window.alby exists → use it (Alby has NIP-07)
2. Else check if window.nostr exists → use it (nos2x / other extension)
3. Else → in-browser keygen (generate PK, store in localStorage, warn user)
```

### Zap Flow (WebLN)

```
1. Reader clicks ZapButton
2. Frontend builds kind 9734 event:
   - p: author's hex pubkey
   - e: article event id
   - a: event coordinate (30023:pubkey:d-tag)
   - relays: [list of relays]
   - amount: chosen by reader (preset amounts: 100, 500, 1000, 5000 sats)
3. Frontend calls window.webln.enable() → Alby popup
4. Alby handles invoice generation + user approval + payment
5. Author's lnurl server publishes kind 9735 to relays
6. Frontend watches for 9735 events with matching e tag → updates zap count
```

### Article Cache Strategy

**Architecture: Browser queries relays directly. Postgres caches the home feed only.**

```
Article page read (/story/:naddr):
  → Browser decodes naddr → nostr-tools queries 4 relays in parallel
  → First valid kind 30023 response wins
  → Rendered immediately (always fresh)
  → NOT routed through Postgres (direct Nostr, no cache)

Home page read (/):
  → Backend has warm cache (Postgres articles table)
  → Returns cached feed instantly (fast, no relay latency)
  → Cache warmed by cron every 5 min

Write path (publish):
  User publishes → sign event → POST to backend
    → Backend verifies signature
    → Backend publishes to relays
    → Backend writes to Postgres (home feed cache)
    → Return naddr to frontend

Update path (edit):
  Same as write, same d-tag, new created_at

Delete path:
  User deletes → sign kind 5 event → POST to backend
    → Backend publishes kind 5 to relays
    → Backend marks article as deleted in Postgres (soft delete)
    → Removed from home feed

Cache warming (cron, every 5 min):
  Backend queries relays for recent kind 30023 events tagged longform
  → Upsert into Postgres articles table
  → Home page always has fresh feed without readers hitting relays
```

---

## Data Models

### Postgres Schema

```sql
-- Articles (cached from Nostr, latest version per d-tag)
CREATE TABLE articles (
  id            TEXT PRIMARY KEY,          -- naddr hash or d-tag
  naddr         TEXT UNIQUE NOT NULL,     -- full naddr for linking
  d_tag         TEXT NOT NULL,            -- article slug
  author_npub   TEXT NOT NULL,           -- author npub
  title         TEXT,
  summary       TEXT,
  content       TEXT,                    -- full markdown (or first 10KB)
  content_hash  TEXT,                   -- SHA256 of content for change detection
  cover_image   TEXT,
  published_at  TIMESTAMPTZ,
  updated_at    TIMESTAMPTZ,
  created_at    TIMESTAMPTZ DEFAULT NOW(),
  zap_count     BIGINT DEFAULT 0,        -- aggregated from 9735
  zap_sats      BIGINT DEFAULT 0,        -- total sats received
  is_deleted    BOOLEAN DEFAULT FALSE,
  first_relay   TEXT,                    -- relay where we first found this
  tags          TEXT[]                   -- array of t tags
);

CREATE INDEX idx_articles_author    ON articles(author_npub);
CREATE INDEX idx_articles_published ON articles(published_at DESC);
CREATE INDEX idx_articles_tags      ON articles USING GIN(tags);

-- Zap events (raw, for aggregation)
CREATE TABLE zaps (
  id          TEXT PRIMARY KEY,
  event_id    TEXT NOT NULL,             -- kind 9735 event id
  article_id  TEXT REFERENCES articles(id),
  author_npub TEXT NOT NULL,
  zapper_npub TEXT,                      -- anonymous if no npub
  amount_sats BIGINT NOT NULL,
  bolt11      TEXT,
  published_at TIMESTAMPTZ,
  UNIQUE(event_id)
);

CREATE INDEX idx_zaps_article ON zaps(article_id);

-- Profiles (cached from kind 0)
CREATE TABLE profiles (
  npub         TEXT PRIMARY KEY,
  display_name TEXT,
  name         TEXT,
  about        TEXT,
  picture      TEXT,
  lud16        TEXT,                     -- Lightning address
  lud06        TEXT,
  website      TEXT,
  updated_at   TIMESTAMPTZ DEFAULT NOW()
);

-- Sessions (web sessions)
CREATE TABLE sessions (
  id          TEXT PRIMARY KEY,
  npub        TEXT NOT NULL,
  created_at  TIMESTAMPTZ DEFAULT NOW(),
  last_seen   TIMESTAMPTZ DEFAULT NOW(),
  ext_auth    TEXT,                     -- 'alby' | 'nostr-key' | 'nos2x'
  FOREIGN KEY (npub) REFERENCES profiles(npub)
);

-- Moderation queue
CREATE TABLE mod_queue (
  id          SERIAL PRIMARY KEY,
  article_id  TEXT REFERENCES articles(id),
  reported_by TEXT,                      -- reporter npub (anonymous if not logged in)
  reason      TEXT,
  status      TEXT DEFAULT 'pending',    -- pending / approved / rejected
  created_at  TIMESTAMPTZ DEFAULT NOW(),
  reviewed_at TIMESTAMPTZ
);

-- Featured articles (curated)
CREATE TABLE featured (
  article_id  TEXT PRIMARY KEY REFERENCES articles(id),
  featured_at TIMESTAMPTZ DEFAULT NOW(),
  note       TEXT                              -- why it was featured
);

-- App config (key-value store)
CREATE TABLE config (
  key   TEXT PRIMARY KEY,
  value JSONB
);
```

### API Endpoints

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| GET | `/api/articles` | No | List articles (filter: tag, author, search, limit, offset) |
| GET | `/api/article/:naddr` | No | Get single article |
| POST | `/api/publish` | Yes | Publish new article (signed event in body) |
| POST | `/api/delete` | Yes | Delete article (signed kind 5 in body) |
| GET | `/api/profile/:npub` | No | Get cached profile |
| PUT | `/api/profile` | Yes | Update own profile |
| GET | `/api/zaps/:articleId` | No | Get zap count for article |
| GET | `/api/featured` | No | Get featured articles |
| POST | `/api/zaps` | No | Receive zap receipt webhook (optional) |
| GET | `/api/me` | Yes | Get current session info |
| POST | `/api/session` | No | Create session (from NIP-07 signed event) |
| DELETE | `/api/session` | Yes | Destroy session |
| GET | `/api/relays` | No | Get configured relay list |
| POST | `/api/report` | No | Report an article |

### Frontend State (React Context)

```typescript
// Session context
interface Session {
  npub: string;
  pubkey: string;           // hex
  displayName?: string;
  picture?: string;
  extAuth: 'alby' | 'nostr-key' | 'nos2x';
  hasLightning: boolean;
  lud16?: string;
}

// Relay status context
interface RelayStatus {
  relay: string;
  connected: boolean;
  latency?: number;
}

// Toast/notification context
interface Toast {
  id: string;
  type: 'success' | 'error' | 'info';
  message: string;
}
```

---

## Build Order

### Phase 0: Foundation
- [ ] Initialize Vite + React + TypeScript project
- [ ] CSS variables + global styles (design tokens)
- [ ] TypeScript types for all Nostr events and API
- [ ] Docker Compose: app + postgres (relay-free)
- [ ] Verify: app builds, runs in Docker, connects to Postgres

### Phase 1: Read
- [ ] NavBar + Footer
- [ ] Home page (static, no data)
- [ ] StoryCard component
- [ ] Article page (hardcoded data)
- [ ] Filter page (hardcoded data)
- [ ] Author profile page (hardcoded data)
- [ ] MarkdownRenderer component
- [ ] Relay query (nostr-tools, browser → relays)
- [ ] Home page → live data from Postgres
- [ ] Article page → live data from Nostr
- [ ] Filter page → live data
- [ ] Author profile → live data
- [ ] ZapCount component (query kind 9735)
- [ ] LoadingSkeleton + ErrorState components
- [ ] EmptyState component
- [ ] Pagination

### Phase 2: Write
- [ ] Login page + all 3 login flows
- [ ] Write page + MarkdownEditor
- [ ] MarkdownPreview
- [ ] Split pane (editor + preview)
- [ ] TagInput component
- [ ] Publish flow: sign → backend → relay
- [ ] localStorage draft auto-save
- [ ] Edit page (pre-fill from existing article)
- [ ] Delete flow (kind 5)
- [ ] Settings page (profile + Lightning address)
- [ ] Profile update (kind 0 event)

### Phase 3: Zap
- [ ] ZapButton component (all states)
- [ ] Zap presets (100/500/1000/5000 sats)
- [ ] Zap flow via WebLN
- [ ] ZapCount live update after zap
- [ ] Author ZapSection (total sats on profile)
- [ ] Zap aggregate on article list

### Phase 4: Polish
- [ ] Import paste (paste markdown → editor)
- [ ] Word count + read time (editor)
- [ ] Cover image URL input + preview
- [ ] Article metadata (word count, date, tags)
- [ ] Related articles (same tags)
- [ ] Share article (copy link, copy naddr)
- [ ] AuthorCard component
- [ ] Responsive design (mobile)
- [ ] Focus states + keyboard nav
- [ ] SEO meta tags per article
- [ ] RSS feed

### Phase 5: Platform
- [ ] Featured articles table + admin UI
- [ ] Report article flow
- [ ] Mod queue page
- [ ] Sitemap
- [ ] Cache warming cron
- [ ] Cache invalidation on edit/delete

---

## Open Questions

| # | Question | Options | Status |
|---|----------|---------|--------|
| 0 | **Relay query architecture** | Direct from browser (no backend) vs via backend | ✅ **Direct from browser — confirmed** |
| 1 | **Markdown editor** | Textarea-based (simple) or CodeMirror/Monaco (rich) | Textarea for MVP |
| 2 | **Image hosting** | External URL only for MVP? Or upload to nostr.build? | External URL only for MVP |
| 3 | **Read time estimate** | 200 wpm or 250 wpm? | 200 wpm (default for long-form) |
| 4 | **Pagination** | Offset-based or cursor-based (faster) | Offset for MVP |
| 5 | **Article content in Postgres** | Full content or just metadata? | Full content (for search) |