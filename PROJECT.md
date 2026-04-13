# Byline — Project Overview

## What is Byline?
A long-form blogging platform built on the Nostr protocol. Writers publish markdown stories to the Nostr network, readers discover content via topic tags. Lightning Network enables readers to zap (tip) authors directly.

## Why Nostr?
- Decentralized, censorship-resistant publishing
- No single platform can deplatform you
- Identity is a cryptographic keypair (npub/nsec) — no username/password silo
- Native Lightning Network integration (zaps) for seamless micro-payments
- Interoperable: any Nostr client can read content published on the network

## Core Principles
- **Markdown-first** — writers write in markdown; the platform handles conversion
- **Key-owned identity** — you own your keys, you own your content
- **Tag-based discovery** — topics are just hashtags; no algorithmic feed
- **Lightning-native** — payments are a core feature, not an afterthought
- **Federated by design** — one tag = one blog; anyone can fork a tag and build their own front-end

## High-Level Architecture Layers

```
┌─────────────────────────────────────────────┐
│  Writer's Browser / App                     │
│  → Markdown editor → sign → publish        │
├─────────────────────────────────────────────┤
│  Nostr Events (NIP-23 long-form, kind 30023)│
│  Stored on relays                            │
├─────────────────────────────────────────────┤
│  Relay Network (public + optional private)  │
├─────────────────────────────────────────────┤
│  Byline Front-end (web app)                 │
│  → Filter by tag → render markdown → zap    │
├─────────────────────────────────────────────┤
│  Lightning Layer (LNURL / Lightning Address)│
│  → Zap reactions (NIP-57)                   │
└─────────────────────────────────────────────┘
```

## Platform Variants
1. **Byline (core)** — general long-form blogging, any topic
2. **Daybook** — personal diary / daybook variant aimed at teens (different safety model, guardian visibility)
3. **Per-topic forks** — any organization can spin up a topic-tag-based blog network under a different tag namespace

## Project Status
- **Phase:** Research & project definition
- **Current step:** 10 foundation questions being defined (2026-04-13)
- **Next:** Research Nostr NIPs, tooling, relay options, Lightning integration

## Folder Structure
```
byline/
├── PROJECT.md          ← you are here
├── agent.md            ← agent instructions for this project
├── questions.md        ← 10 foundation questions + answers (living doc)
├── research/           ← research notes, tool evaluations
│   ├── nostr-nips.md
│   ├── lightning.md
│   └── tooling.md
└── SPEC.md             ← project spec (filled after questions answered)
```
