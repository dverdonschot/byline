# Research: Alby & NWC Integration

**Status:** [CONFIRMED]

## The Three Alby Integration Points

### 1. NIP-07 Browser Signing (window.nostr)
Alby injects `window.nostr` into every page when the extension is active:
```javascript
// Check if Alby/nostr extension is available
if (window.nostr) {
  const pubkey = await window.nostr.getPublicKey()
  const signedEvent = await window.nostr.signEvent({
    kind: 30023,
    content: markdownContent,
    tags: [['t', 'byline'], ['d', 'article-slug']],
    created_at: Math.floor(Date.now() / 1000)
  })
}
```
Byline never sees the private key. User approves signing via Alby popup.

### 2. WebLN (window.webln) — Lightning Payments
Used when Byline readers want to zap:
```javascript
if (window.webln) {
  await window.webln.enable()
  // Alby handles the invoice flow internally
  // We get back the invoice result
}
```
Reader must have Alby extension. Works without Byline having any Lightning infrastructure.

### 3. NWC (Nostr Wallet Connect) — for users without Alby extension
Users who don't have the Alby extension can use NWC to connect any Lightning wallet:
```javascript
import { webln } from "@getalby/sdk"
const nwc = new webln.NWC({ nostrWalletConnectUrl: "nostr+walletconnect://..." })
await nwc.enable()
// Now has Lightning access via their own wallet
```
NWC connects to Alby's NWC relay or a self-hosted NWC service.

## Login Flow Summary

| User has | Login method | Lightning access |
|----------|-------------|-----------------|
| Alby extension | NIP-07 (`window.nostr`) | WebLN (instant) |
| No extension, has Lightning wallet | NWC OAuth | Via wallet's NWC |
| No extension, no wallet | In-browser keygen | Alby Hub account |

**Recommended priority:** NIP-07 (Alby) first, NWC second, in-browser keygen as fallback.

## What Byline Needs from Alby

1. **For readers:** `window.webln.enable()` → handles zap flow automatically
2. **For writers:** `window.nostr.signEvent()` → sign kind 30023 events
3. **For Alby accounts without extension:** NWC connection string from Alby Hub

## Zap Split Architecture

NIP-57 supports multi-recipient zaps via multiple `p` tags with weights. For Byline platform cut:
- Author sets Lightning Address in profile (lud16)
- If Byline takes a cut, author must configure a split — or Byline runs as a relay that takes a percentage
- **Simplest path:** No platform cut initially. Just let zaps go 100% to author.

## Habla News Reference

Habla News (habla.news) is the best reference for Byline's exact use case:
- Long-form content (NIP-23 kind 30023)
- Lightning zaps (NWC)
- NIP-07 signing (Alby/nos2x)
- Next.js frontend
- Curated featured page

Checking their tech: they use Next.js + NDK-like relay querying + Alby SDK for Lightning.
