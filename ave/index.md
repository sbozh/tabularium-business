---
title: AVE Rebuild Kit
tags:
  - ave
  - ludus
  - kit
---

# AVE Rebuild Kit

The concept set for rebuilding AVE at `ludus/apps/ave`. Goal, in one sentence:
**a chat with an avatar whose brain is a connected Claude Code session** —
ending in a conversation with [[magistratus|Magistratus]], the business-logic
controller.

This kit documents what AVE learned the hard way, trimmed to what ludus needs.
It is written for two readers at once: a human deciding *what to keep*, and a
coding agent building *from these notes alone*. No big code blocks — concepts,
contracts, and the reasons behind them.

## Scope decided

- **In**: [[messenger-core]] · [[avatar-identity]] · [[brain-connection]] ·
  [[storage-seam]] · [[tabularium-subsystem]] · [[magistratus]]
- **Out** (deliberately, for v1): Apollo guidance channel, LCB/worktree
  frontends, the empire/podman layer, CSP. They solve problems ludus does not
  have yet; every one can be added behind existing seams later.
- **Runtime**: local-first — SQLite file + local web app + MCP tools — with the
  storage layer kept as a driver seam so a Cloudflare D1/Worker port is
  mechanical, not a rewrite ([[storage-seam]]).

## Build order

1. [[storage-seam]] — the chat DB contract first; everything else writes
   through it.
2. [[messenger-core]] — threads, messages, presence. The app you can open.
3. [[brain-connection]] — MCP tools that let a Claude Code session *be* an
   avatar. The moment chat answers back, the core is done.
4. [[avatar-identity]] — the roster; Magistratus becomes a persona, not a
   prompt.
5. [[tabularium-subsystem]] — the knowledge vault loop (draft → commit →
   publish). Must-have: this kit itself lives by it.
6. [[magistratus]] — the end goal: the controller in the chat.

## The one law to carry over

AVE's deepest rule survives translation: **the record is the DB, not the
process**. Every lesson in these notes — presence honesty, close semantics,
session ownership — comes from moments when in-memory state pretended to be
the record and lied. Ludus already knows this law from its own engine ("compose
context → call pure function → persist → notify"). Keep it.
