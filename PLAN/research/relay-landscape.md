# Research: Relay Landscape

**Status:** [CONFIRMED — general picture clear, specifics need testing]

## Public Relays That Support kind 30023

Most well-known public relays support kind 30023 as it's part of the standard NIP set:

| Relay | URL | Notes |
|-------|-----|-------|
| relay.damus.io | wss://relay.damus.io | Damus default, well-maintained |
| nostr.land | wss://nostr.land | Popular public relay |
| purplepag.es | wss://purplepag.es | Supports outbox model |
| nos.lol | wss://nos.lol | Nostr Build's relay |
| relay.nostr.bg | wss://relay.nostr.bg | Good uptime |
| wss://nostr.fmt.wiz.biz | wss://nostr.fmt.wiz.biz | Various NIPs supported |

**Check relay policies at:** https://api.nostr.watch/v1/online (real-time relay list with NIP support)

## Relay Software Options (for Byline's future own relay)

| Software | Language | Notes |
|----------|----------|-------|
| **nostr-rs-relay** | Rust | High performance, single binary, good for self-hosting |
| **Strfry** | C++ (golpe) | Used by nostr.watch, performant, no persistence DB (in-memory) |
| **Nostream** | Node.js/TypeScript | Most accessible for JS developers, SQLite persistence |
| **Shugur Relay** | SvelteKit + Tailwind | Production-ready, enterprise-grade, built on nostr-rs-relay core |

## Storage Policies

- Most public relays have **size limits** (typically 64KB–256KB per event)
- Long articles with cover images may hit these limits — Byline should recommend keeping articles under ~50KB content
- Images should be hosted externally (NIP-94 for file headers) rather than in event content
- Relay retention varies — some keep forever, some have time-based eviction

## Recommended Starting Relays for Byline

```
wss://relay.damus.io
wss://purplepag.es
wss://nos.lol
wss://relay.nostr.bg
```

These 4 give good coverage for kind 30023 long-form content. Multi-relay publish for redundancy.

## Discovery / Indexing

Currently no dedicated long-form search engine, but:
- **nostr.watch** — relay-level health monitoring
- **nostr.band** — event search (including kind 30023)
- **NIP-51** — specialized relay for filtered content (future Byline relay option)

## Implications for Byline

1. **No own relay initially** — use 4-6 public relays as Dennis said
2. **Watch relay policies** — some relays may go offline; Byline should track relay health
3. **Image hosting** — need external image hosting (NIP-94 or third-party)
4. **Medium-term** — consider a NIP-51 specialized relay for Byline content filtering
