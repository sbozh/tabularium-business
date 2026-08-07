---
title: Storage Seam
tags:
  - ave
  - kit
  - storage
---

# Storage Seam

The decision that makes everything else portable: **all chat state behind one
narrow storage contract**, SQLite today, D1 tomorrow.

## The contract

One module owns every query. Its surface is domain verbs, not SQL: append
message · mark answered · inbox for avatar · thread history · beat heartbeat ·
presence for avatar · open/close run. Callers never touch the driver; the
driver never leaks upward. AVE's `chat-db` proved the verb *vocabulary* —
deliberately driver-agnostic across two SQLite drivers — but never fully
enforced the seam: raw queries leaked into the API plugin and tool layer.
The kit's rule ("no query outside the seam") is what closes that gap. And one
contract decision AVE cannot hand down: **make the surface async from day
one**. AVE's verbs are synchronous (in-process SQLite); D1's binding API is
Promise-based, so a synchronous contract is the one thing a D1 executor can
never implement.

## Why local-first, why the seam

Local-first (a SQLite file next to the app) is the fastest road to the chat
feeling: no deploy, no auth, no network between the agent and the record. And
ludus is *built* as Cloudflare Workers + D1 per tenant — though today's live
tenant runs those Workers self-hosted in containers, with the D1 data on a
server volume. Either way the seam rule holds: **write no query outside the
seam**, and the port is "implement the same contract on D1's executor, point
the app at it" — the logic itself never moves.

## Single writer, honest reads

AVE's worst outage was self-inflicted: two *machines'* views of one SQLite
file (host tooling and a container, across a VM mount) poisoned the WAL index
— reads started failing "malformed" while the bytes were fine. The lesson
generalizes:

- **One process writes a given DB file.** Everyone else goes through that
  process's API (HTTP or MCP), never the file.
- Inspect through the app, not around it. A debugging shortcut against the
  live file is how the outage started.
- D1 enforces the harder half by construction: nobody touches a file, every
  query goes through a binding. One-Worker ownership stays a discipline there
  as here — which is the quiet argument that the seam's discipline is the
  real invariant, not the driver.

## Simpler in the rebuild

- **Migrations in the open.** AVE grew its schema by scattered `CREATE TABLE
  IF NOT EXISTS` plus ad-hoc `ALTER` checks. The rebuild keeps one ordered
  migration list the seam applies on open — the same list later feeds D1's
  migration tooling.
- **Session/event rows are part of the same contract.** Heartbeats, run
  open/close, verdict records ([[magistratus]]) — same DB, same seam, so "the
  record is the DB" stays one sentence with no exceptions.
- **No second store.** AVE still keeps registries beside the DB (in-memory
  session maps, env snapshot files) — and the ones that mattered kept
  disagreeing with the record. The rebuild keeps none: if it matters, it is
  a row.
