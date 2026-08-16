---
title: Voice-to-Lead — the pipeline Itinerarium diagrams first
tags:
  - ludus
  - itinerarium
  - infrastructure
  - planning
  - epic
---

# Voice-to-Lead — the pipeline [[index|Itinerarium]] diagrams first

Sales calls happen across whatever CRM and telephony a tenant already runs.
Today ludus distributes leads that already exist as CRM records — the gap is
the call itself. This note is the reasoning [[index|Itinerarium]]'s first
diagram renders, and it is the Epic that funds the worktree campaign to build
that diagram, the way `ave/magistratus` funds the AVE rebuild campaign today.

*Простыми словами, по-русски: [[../dude/itinerarium/voice-to-lead|dude/itinerarium/voice-to-lead]].*

## The idea

Transcribe the call, diarize the speakers, extract intent / contact / next
step from the transcript, and hand the result to ludus's existing
pipeline-and-adapter machinery so it gets distributed and routed exactly like
any other lead — across whichever CRM the tenant runs (Kommo today, others
by the same adapter family). The call becomes a lead source, not a
transcript nobody reads twice.

## Reference points researched

Not vendors being adopted — reasoning material the diagram's nodes draw on.

- **Mistral Workflows API** (`docs.mistral.ai/api/endpoint/workflows`) — an
  orchestration model: versioned "registrations" of a workflow, executed
  sync or async with timeout control. The lesson: a voice→lead pipeline can
  be one workflow with named steps (transcribe → diarize → extract → adapt →
  distribute) instead of bespoke glue code stitched per step.
- **Clay** (`clay.com/pricing`) — a GTM data-enrichment platform: 150+
  data-provider "waterfalls," AI research agents (Claygent), CRM sync-back.
  The lesson: enrichment-as-waterfall — try a strict extractor, fall back to
  a looser one — is a reusable shape for pulling structured lead fields out
  of a raw transcript.
- **MOSS-Transcribe-Diarize**
  (`huggingface.co/OpenMOSS-Team/MOSS-Transcribe-Diarize`) — a 0.9B
  Apache-2.0 model doing joint transcription + speaker diarization in one
  pass: audio/video up to 90 minutes in, timestamped speaker-labeled text
  out (`[Sxx]` tags), 50+ languages, hotword prompting for domain terms,
  competitive character-error-rate against GPT-4o and Gemini on its
  benchmarks. A concrete, self-hostable candidate for the diagram's
  "transcribe + diarize" node — it removes the need for a separate ASR
  pipeline and a separate diarization pass.
- **salesup.tech** — the closest thing that already exists to what is being
  sketched here: call-monitoring/analytics that transcribes every sales
  call, flags risk and opportunity, profiles managers, and already
  integrates with HubSpot, Pipedrive, Kommo and others. Its angle is
  analytics and coaching, told *after* the call, *to* a manager. Ludus's
  angle is different and complementary: act *on* the call's content by
  turning it into a routed, distributed lead — not a coaching insight. The
  diagram should show salesup.tech's shape next to ludus's addition so the
  difference is visible on the page, not left implied.

## Where this lands in ludus's architecture

For the diagram's node labels — reasoning only, not a restatement of
`CLAUDE.md`'s architecture pillars:

- A transcript-derived lead is a new producer feeding the existing
  `ingestLead` / `onLeadEvent` adapter contract (the CRM Adapter pillar,
  `packages/core`) — "just another adapter." Nothing downstream needs to
  know a lead originated from a phone call instead of a webhook.
- Transcribe → diarize → extract is a Bridge composition
  (`internal-call` / `queue` transports) — no new architectural pillar is
  needed, this is existing pillars composed in a new order.
- The Nash forecast/rebalance loop is unaffected: a call-derived lead
  distributes through the same deterministic scoring path as any other
  lead once it reaches the engine.

## Not decided — placeholders, not rulings

- Which transcription/diarization vendor or model ludus actually runs on.
- Whether extraction is a Mistral-hosted workflow or ludus's own Worker.
- Which CRMs beyond Kommo get a voice-sourced adapter first.
- Real-time (streaming, mid-call) versus after-call batch processing.

These stay open until a worktree interview turns one into a decision — this
note records the reasoning, not the ruling.
