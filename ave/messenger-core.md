---
title: Messenger Core
tags:
  - ave
  - kit
  - chat
---

# Messenger Core

The chat model that makes "talking to an avatar" feel like a messenger, not a
form that calls an API. Three nouns carry the whole design.

## The model

- **Location** — *where* a conversation happens. In AVE: Rome (the root
  project) or a worktree. In ludus it can start as a single location ("Ludus")
  and grow. Locations are the tabs of the UI.
- **Avatar** — *who* speaks there. A separate axis from location. The same
  location can host different avatars ([[avatar-identity]]).
- **Thread** — the unit of history: **(location × avatar)**. Messages belong
  to a thread; switching avatars in a place is switching threads, not losing
  them.

A **run** is an ephemeral brain connection to a thread — a Claude Code session
attaching, answering, detaching. Runs come and go; threads persist. This split
(persistent thread, ephemeral run) is what makes the chat feel alive without
pretending the agent is always there.

## Presence must be honest

The green dot is earned, never assumed. An avatar is *online* iff its brain
heartbeat row is fresh (AVE beats every ~4s from inside the MCP server process,
so the beat dies exactly when the session dies — and the UI honestly flips to
"connection lost"). Presence derived from anything else — a config flag, a
recent reply, hope — eventually lies to the user.

Message states are equally honest: a user message is **unanswered** until an
avatar's reply row marks it answered. The inbox is *the query* "unanswered
messages addressed to avatar X", not a separate mailbox structure to keep in
sync.

## The loop

1. Human types in the chat UI → message row (sender `user`, thread T).
2. The brain's watch notices the unanswered row ([[brain-connection]]).
3. The agent drains the inbox, replies **as the avatar** → reply row, original
   marked answered.
4. UI renders the thread from rows. Nothing is in memory that is not in the DB.

## Simpler in the rebuild

- **One writer per concern.** In AVE the API plugin grew into a god-file
  (sessions, presence, process-killing, context bundles, port translation in
  one place). Split by responsibility from day one: a chat API, a presence
  probe, an admin surface — each its own module behind one storage contract.
- **Close is a record operation.** AVE's `ave_close` could only close sessions
  its own process had spawned — a restarted server couldn't close anything,
  and ghosts lingered. Closing a thread/run must be a DB status flip that any
  process can perform; killing processes is a separate, optional act.
- **No self-registration dance.** AVE's LCB notify/registry indirection exists
  for multi-frontend worktrees. With one app and one DB, the UI reads threads
  straight from storage — delete the middleman until a second frontend exists.
