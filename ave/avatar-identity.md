---
title: Avatar Identity
tags:
  - ave
  - kit
  - avatars
---

# Avatar Identity

An avatar is a persona with a contract, not a system prompt pasted into a chat.
AVE's roster gives each one four things; keep all four in the rebuild.

## The four parts

- **Identity** — a `ME.md` per avatar: who they are, their voice, their
  station. The file is *source*, read at context-build time; it is never
  written back by agents. (Ludus already lives this split: governance files are
  source, state lives elsewhere.)
- **Register** — how the avatar speaks *to whom*. AVE's oKtavian speaks as a
  servant of the Republic to the human and as Emperor to other avatars. For
  [[magistratus|Magistratus]] the register is judicial: formal verdict language
  in rulings, plain speech in conversation.
- **Knowledge** — declared sources, loaded as excerpts when the avatar's
  context is built. In AVE today governance files and skills are actually
  excerpted; vault notes are declared but arrive as pointers (retrieval is
  pending). Declared, not scraped: the avatar knows what it was given, and
  the list is auditable. The rebuild should finish the vault half — the
  Quartz link graph ([[tabularium-subsystem]]) is the intended index.
- **Brain preference** — which model/agent animates it, with fallback. The
  roster entry says *prefer*, reality decides: a brain is whatever is currently
  beating the heartbeat ([[brain-connection]]).

## Why identity is data, not code

The base mechanism in AVE is data-only: drop a `ME.md` and the roster scan
picks the avatar up, zero code. But AVE has already leaked once — Marcus grew
avatar-specific endpoints, a dedicated UI card, and an entire dashboard app.
That leak is the cautionary half of the lesson: ludus should mint a scriba, a
lanista, or a magistratus as *entries* and retire them the same way. The
moment an avatar needs its own endpoint or its own table, the abstraction has
leaked again.

## Simpler in the rebuild

- **One roster, one shape.** AVE's scan merges three filesystem sources
  (`ME.md` identities, skill-file avatars, project manifests); three sources
  of truth drift. Keep a single roster (a directory of `ME.md` files *is*
  enough — the scan is the registry).
- **Context assembly is a pure function.** roster entry + declared knowledge +
  thread history → context bundle. No I/O decisions inside; the caller fetches,
  the function composes. This mirrors ludus core's own discipline (pure
  `distribute(ctx)`), and it makes avatar behavior replayable.
- **Voice belongs to the avatar, not the tool.** Reply formatting, register
  switching, signature lines — all live in the identity layer, so the MCP
  reply tool stays a dumb pipe ([[brain-connection]]).
