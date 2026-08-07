---
title: Brain Connection
tags:
  - ave
  - kit
  - mcp
---

# Brain Connection

How a Claude Code session becomes an avatar's brain. This is the part that
turns a chat UI into a conversation — and the part with the most scar tissue.

## The contract (three verbs and a pulse)

- **`inbox_poll`** — "give me unanswered messages addressed to avatar X."
  In AVE this doubles as a presence write (each call records a poll marker
  that counts toward liveness). The rebuild should make it a pure query and
  let the heartbeat alone carry presence — cleaner, and the event log stops
  growing on every poll.
- **`reply`** — "as avatar X, answer message N with this body." Persists the
  reply, marks the message answered. The only write path for an avatar
  *answering its own inbox* (visiting-avatar speech has its own verb).
- **`attach`** (brain) — start the heartbeat for avatar X. The pulse is
  written from inside the MCP server process every few seconds, so it stops
  exactly when the session stops. Presence = beat-row freshness (AVE also
  stamps the session id on each beat so the UI can verify *which* run is
  alive) — nothing else ([[messenger-core]]).
- Everything else (open/close/list threads) is bookkeeping around these three.

## The auto-answer loop

A session that *is* a brain should answer without being asked. AVE's working
pattern: a lightweight background watch polls the inbox (via the same MCP
surface over HTTP) and wakes the agent only when a new unanswered message
appears; the agent then drains and replies in voice. The watch is dumb — dedup
by message id, one notification per new message; all intelligence stays in the
agent.

## Scars worth inheriting

- **Stateless bridge or bust.** AVE's HTTP MCP bridge originally kept
  per-connection state and died on every reconnect. Stateless request/response
  survived reconnects, session restarts, even the server being rebuilt
  mid-conversation. Ludus should start stateless: any session can pick up any
  avatar at any time.
- **Ownership must be explicit.** AVE's server booted a "standing" root
  session for itself, and a connecting client then found *something alive* it
  couldn't distinguish from a real user session — a deadlock until a
  supersede rule was added. The rebuild's rule: every run row says *who owns
  it* (a client session or the server itself), and a placeholder is always
  superseded by a real client without ceremony.
- **The record survives the process.** A brain restart must be able to re-adopt
  an open thread by reading the DB — never by trusting an in-memory map. AVE's
  revive-on-startup exists because the first version lost the pulse on every
  reconnect and the UI showed false death.

## Simpler in the rebuild

- Collapse AVE's tool zoo (init/update/join/navigate/…) to the messenger
  contract above plus one `open`/`close`. Grow verbs when a feature demands
  one, not before.
- One transport: HTTP MCP from the start (local port). Know what this trades
  away: AVE's *primary* transport is stdio — the server is a child of the
  Claude Code session, which is what makes the heartbeat die with the session
  for free. An HTTP-only server owns its lifecycle explicitly (start/stop,
  and a pulse per attached brain rather than per process). One door, but the
  door must now close itself.
