# Longform — Project Overview

## What is Longform?
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

## Platform Variants
1. **Longform (core)** — general long-form blogging, any topic
2. **Daybook** — personal diary / daybook variant aimed at teens (different safety model, guardian visibility)
3. **Per-topic forks** — any organization can spin up a topic-tag-based blog network under a different tag namespace

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
│  Longform Front-end (web app)              │
│  → Filter by tag → render markdown → zap   │
├─────────────────────────────────────────────┤
│  Lightning Layer (LNURL / Lightning Address) │
│  → Zap reactions (NIP-57)                 │
└─────────────────────────────────────────────┘
```

## Project Status
- **Phase:** Research complete, architecture defined
- **Current step:** Waiting on domain check (Dennis), then move to build
- **Next:** Docker setup, tech stack (Vite + Node.js), MVP build
- **Confirmed:** Name=Longform, Tech=Vite+Node.js+Express, MVP=Docker self-hosted, Auth=Alby+Nostr keys

## Folder Structure
```
longform/
├── PROJECT.md          ← you are here
├── SPEC.md             ← full technical specification
├── agent.md            ← research agent instructions
├── questions.md        ← 10 foundation questions + answers
├── next-questions.md   ← 10 phase-2 questions (answers in SPEC.md)
├── research/           ← research notes, tool evaluations
└── SPEC.md             ← project spec
```
