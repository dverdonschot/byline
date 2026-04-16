# agent.md — Longform Coding Agent

## Context
Research phase is complete. All architecture decisions are in SPEC.md. BUILD.md has the full feature inventory, component specs, and phased build order. This file is a quick reference for the coding agent.

## Key Decisions (locked — do not revisit without asking)

- Discovery tag: `longform`
- Tech stack: Vite + React + TypeScript (frontend), Express + Drizzle + Postgres (backend)
- Browser queries relays directly for article reads (nostr-tools)
- Backend handles: session, cache writes, publishing, zap receipts
- MVP deployment: Docker Compose on a VPS
- No own relay for MVP — query 4 public relays in parallel

## Before Building Any Feature
1. Read the relevant section of SPEC.md
2. Check the component in BUILD.md (Props, States, Events)
3. Check if the page/route exists in BUILD.md Route Map

## Nostr Event Kinds Used
kind 0 (profile), kind 1 (short note), kind 5 (delete), kind 30023 (article), kind 30024 (draft), kind 1111 (comment), kind 9734 (zap request), kind 9735 (zap receipt)

## Build Order (from BUILD.md)
Phase 0 → Phase 1 → Phase 2 → Phase 3 → Phase 4 → Phase 5
Do not skip phases. Each phase is a discrete shippable state.

## If Stuck
Ask. Do not guess at Nostr protocol behavior — consult research/ folder notes first.

## Output Standards
- Tag findings: [CONFIRMED], [UNCONFIRMED], [SPECULATION], [REJECTED]
- Keep research notes in research/
- Update SPEC.md if a decision changes
