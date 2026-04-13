# Research: NIP-23 Long-Form Content

**Status:** [CONFIRMED]

## Event Structure (kind 30023)

```json
{
  "kind": 30023,
  "created_at": 1675642635,       // updated on edit
  "content": "Markdown text...",  // plain text markdown
  "tags": [
    ["d", "article-slug"],         // unique identifier (for editing)
    ["title", "Article Title"],
    ["published_at", "1296962229"],
    ["t", "tag1"],                 // topics (can be multiple)
    ["t", "tag2"],
    ["image", "https://..."],      // cover image URL
    ["summary", "..."],
    ["e", "<event-id>", "<relay>"],  // references (optional)
    ["a", "30023:<pubkey>:<d>", "<relay>"] // addressable event ref
  ],
  "pubkey": "...",
  "id": "...",
  "sig": "..."
}
```

## Key Points

- **kind 30023** is the standard long-form article event. **kind 30024** is for drafts.
- Content is **plain Markdown text** — no HTML, no hard line breaks
- Articles are **editable** — use `d` tag as unique identifier; `created_at` = last update time
- References to other notes/articles use NIP-27 `nostr:` links
- Replies use **kind 1111** (NIP-22 comments)
- Addressable via NIP-19 `naddr` code

## Tags Used
| Tag | Purpose | Required |
|-----|---------|----------|
| `d` | Article slug/identifier | ✅ |
| `title` | Article title | recommended |
| `published_at` | First publish timestamp (Unix seconds) | recommended |
| `t` | Topic hashtags (multiple allowed) | for discovery |
| `image` | Cover image URL | optional |
| `summary` | Article summary | optional |

## Editing Behavior
- Edits create a **new event** with updated `created_at` — old versions may still exist on relays
- Clients should use `d` tag + `created_at` to show latest version
- NIP-23-aware relays handle this; non-aware relays may show old versions

## Multi-Tag Support
- Multiple `t` tags are allowed and standard — one article can appear under multiple topics
- This is how Byline can show content across different tag filters

## Implications for Byline
1. **We follow NIP-23 exactly** — Byline stores and retrieves kind 30023 events
2. The `d` tag is our article ID — used for editing, replying, referencing
3. Multiple `t` tags = article appears in multiple topic filters
4. We should set up a dedicated Byline tag (e.g., `byline`) and encourage authors to tag with it
5. Images are URLs — Byline needs an image hosting strategy (external or nostr-native via NIP-94)
6. Content is Markdown — we render client-side or server-side with a markdown parser
