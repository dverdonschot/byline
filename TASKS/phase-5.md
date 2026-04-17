# Phase 5 — Platform

**Goal:** Byline operates as a real platform — featured content curation, moderation, and operational health.
**Started:** ⏳
**Completed:** ⏳

## Prerequisites
- Phase 4 fully complete

---

## Tasks

### 5.1 — Featured Page
- [ ] `featured` table in Postgres stores featured article IDs
- [ ] Admin-only UI to add/remove featured articles
- [ ] Featured articles shown in dedicated section on home page
- [ ] Featured badge on ArticleCards

### 5.2 — Admin UI
- [ ] `/moderation` route — visible only to admin npub (configurable in `.env`)
- [ ] List all reported articles with reporter reason and timestamp
- [ ] Actions per report: Approve (remove from queue), Reject (keep featured), Delete (remove from platform)
- [ ] Bulk actions: select multiple, apply action to all selected

### 5.3 — Report Article Flow
- [ ] "Report" button on article page (logged-in users only)
- [ ] Modal: select reason (Spam / Harmful / Other), optional text
- [ ] Submit: write report to `mod_queue` table via backend API
- [ ] Confirmation: "Report submitted. Our team will review shortly."

### 5.4 — Mod Queue
- [ ] Backend: `GET /api/mod/queue` — list pending reports
- [ ] Backend: `POST /api/mod/action` — approve/reject/delete
- [ ] Mod actions write to `mod_queue` table (status update)
- [ ] On delete action: sign and publish kind 5 event, soft-delete in Postgres

### 5.5 — Cache Warming Cron
- [ ] Backend cron: runs every 5 minutes
- [ ] Queries relays for recent kind 30023 events tagged `longform`
- [ ] Upserts into `articles` table in Postgres
- [ ] Fetches latest zap counts for updated articles
- [ ] Invalidates Redis cache for home feed after each run
- [ ] Logs run to `cron_log` table (success/failure, duration, articles found)

### 5.6 — Cache Invalidation
- [ ] On article publish: immediately upsert to Postgres, invalidate Redis home feed cache
- [ ] On article edit: update Postgres, invalidate Redis cache
- [ ] On article delete: soft-delete in Postgres, invalidate Redis cache
- [ ] On profile update: invalidate Redis profile cache

### 5.7 — Sitemap
- [ ] `GET /sitemap.xml` — dynamic sitemap
- [ ] Includes: home page, filter page, all article pages (naddr-encoded)
- [ ] Updates on publish/edit/delete
- [ ] Submitted to Google Search Console (manual step — document in SHIPPING.md)

### 5.8 — Email Digest (Optional / Deferred)
- [ ] If time permits: weekly email digest of featured articles
- [ ] Subscribe button on home page (stores email in Postgres)
- [ ] Send via configured SMTP (ENV vars: SMTP_HOST, SMTP_USER, SMTP_PASS)
- [ ] Unsubscribe link in every email

---

## Exit Criteria (Platform Complete When)
- [ ] Featured articles appear on home page
- [ ] Admin can manage mod queue via UI
- [ ] Users can report articles
- [ ] Reports appear in mod queue
- [ ] Cache warming runs every 5 minutes without failure
- [ ] Home page feed is fresh without requiring relay queries from the browser
- [ ] Sitemap is valid and covers all published articles
