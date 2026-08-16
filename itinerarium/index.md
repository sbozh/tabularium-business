---
title: Itinerarium
tags:
  - ludus
  - kit
  - itinerarium
  - infrastructure
  - planning
---

# Itinerarium

A mind-map-style diagram viewer inside ludus (`apps/itinerarium`) that shows
the planned infrastructure as connected nodes — the same way a Roman
itinerarium mapped roads and the waypoints along them across the empire.
First subject on the map: [[voice-to-lead|Voice-to-Lead]], the pipeline that
turns a sales call into a routed, distributed lead.

This kit is planning only. No transcription, no diarization, no CRM writes —
diagrams that describe intent, for a human and a future coding agent to read
before any of it is built.

*Простыми словами, по-русски: [[../dude/itinerarium/index|dude/itinerarium/index]].*

## Why the name

Roman *itineraria* (the *Itinerarium Antonini*, the *Itinerarium
Burdigalense*) were route-maps: waypoints and the roads connecting them
across the empire. That is the closest Latin concept to "an infrastructure
roadmap diagram" — closer than a generic *mappa* or *tabula* would be — and
it does not collide with vocabulary already spoken for: [[../ave/magistratus|
Magistratus]] judges, Lanista reviews architecture, Scriba files issues,
Ovidius commits and releases, Legatus owns a worktree, Castra raises the
camp, Tabularium is the archive itself. Itinerarium is simply the map.

## Scope decided

- **In**: SVG / mind-map diagrams of *planned* infrastructure — dark-themed
  node-and-arrow style (boxes, decision diamonds, colored arrows on a navy
  ground, in the spirit of a GoJS or Miro flowchart), starting with
  [[voice-to-lead]].
- **Out** (deliberately, for v1): no real transcription/diarization
  integration, no live data feeding the diagrams, no actual CRM writes. A
  diagram that quietly started doing something live would stop being a plan
  and start being an undocumented feature — that trade is not made here.

## Build order

1. [[voice-to-lead]] — the reasoning note; the Epic that funds the campaign.
2. Diagram content and information architecture for the viewer (which
   diagrams, which nodes, what a click reveals) — drafted in the worktree
   interview, not this note.
3. `apps/itinerarium` scaffold — a future campaign, not this session.
4. Wiring real infrastructure behind a diagram, node by node — later still,
   and each node's build is its own campaign against its own Epic.

## The one law to carry over

A diagram is a claim. [[voice-to-lead]] marks what is decided fact versus
placeholder for exactly this reason — the same discipline HANDOFF.md holds
every campaign order to. A mind map that mixes "decided" and "someone's
guess" with no visual difference is worse than no mind map.
