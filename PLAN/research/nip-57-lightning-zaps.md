# Research: NIP-57 Lightning Zaps

**Status:** [CONFIRMED — well specified]

## The Two-Event Model

| Kind | Name | Direction | Published? |
|------|------|-----------|------------|
| 9734 | Zap Request | sender → recipient's lnurl server | ❌ No (HTTP GET only) |
| 9735 | Zap Receipt | recipient's lnurl server → relays | ✅ Yes |

## The Zap Flow (simplified)

```
Reader clicks "Zap" on Byline article
         │
         ▼
Client creates kind 9734 event (zap request)
  - includes: p (recipient pubkey), e (article event id), a (event coord), relays
  - content: optional zap message
         │
         ▼
Client sends 9734 event as HTTP GET to recipient's lnurl callback URL
  - params: amount (msats), nostr (JSON-encoded 9734), lnurl
         │
         ▼
Recipient's lnurl server validates the 9734 event signature
  - Server needs nostrPubkey configured (to sign zap receipts)
  - Server generates invoice with zap request as description hash
         │
         ▼
Client receives invoice (bolt11)
         │
         ▼
Reader's Lightning wallet pays the invoice
         │
         ▼
Recipient's lnurl server publishes kind 9735 (zap receipt) to relays
  - Relay list from the 9734 event
  - Zap receipt contains: bolt11 tag, description (the 9734 JSON), preimage
         │
         ▼
All Nostr clients watching relays see the 9735 and display the zap
```

## What an Author Needs to Receive Zaps

To receive zaps, an author needs:

1. **A Lightning Address** (e.g., `dennis@byline.so`) — or an **LNURL pay endpoint**
2. **An lnurl-pay server** that:
   - Serves a `.well-known/lnurlp/<username>` endpoint
   - Has `nostrPubkey` configured (the pubkey that signs zap receipts)
   - Has `allowsNostr: true` in its response
   - Has a `callback` URL that receives the zap request
3. **The `lnurl` or `lud16` field in their Nostr kind-0 profile** pointing to the lnurl endpoint

## Zap Splits (Multiple Recipients)

NIP-57 supports **zap splits** via multiple `p` tags with optional weight (4th parameter = percentage):

```json
["p", "<pubkey1>", "<relay>", "90"],  // 90% to author
["p", "<pubkey2>", "<relay>", "10"]   // 10% to Byline platform
```

Clients calculate percentages from weights and divide the zap accordingly.

## Can We Avoid a Full Lightning Node?

**Yes.** Options:

1. **Alby Hub** — managed Lightning wallet with Nostr support. Author connects Alby, gets Lightning address, Alby handles the lnurl server. Easy but custodial (you trust Alby).

2. **Zap Pool** (zappool.com) — service that acts as your lnurl server. You connect your own node or use their managed wallet. Good middle ground.

3. **Self-hosted lnurl server** — run [cln-nostr-zapper](https://github.com/jb55/cln-nostr-zapper) (reference implementation) with a Core Lightning node. Full control but requires node management.

4. **Lightning Address via DNS** — any Lightning address provider (Alby, LightningAddress.com, etc.) gives you an lnurl endpoint.

## Primal's Approach

Primal uses **WebLN + Alby integration** for one-tap zaps:
- Users authenticate with Alby (browser extension or Alby account)
- Primal uses WebLN to request invoice from Alby
- Alby pays the invoice from user's connected Lightning wallet
- Recipient needs only a Lightning Address in their profile

**Key insight:** Primal doesn't run the author's lnurl server — they rely on Alby's infrastructure and the recipient having an Alby account or compatible Lightning address.

## Implications for Byline

1. **For authors:** We need them to have a Lightning Address. We should support Alby, and any lud16/lud6 Lightning address.
2. **For readers:** We need to support WebLN (Alby browser extension) OR a wallet selector. NWC (Nostr Wallet Connect) is another option for Alby-less users.
3. **Zap receipt handling:** We need to watch for kind 9735 events on our watched relays and aggregate zap totals per article.
4. **Zap splits:** Technically possible with NIP-57. Defer until Byline has revenue model.
5. **Platform approach:** Use Alby as the default lnurl provider for authors (easiest UX), with option to use custom Lightning Address.
6. **Invoice generation:** We don't need to generate invoices — the author's lnurl server does that. We just need to pass the zap request to it.
