---
title: Developments
tags:
  - ludus
  - governance
  - developments
---

# Developments

The register of what may be worked on. **This note is the only list
[[../ave/magistratus|Magistratus]] watches** — he binds one session to one
entry here and refuses anything this table does not name. A project missing
from the register is not a small exception: it is unregistered, and the
answer is to record it here first.

Each entry is a **line of development**: a branch that receives work, with
its own scope and its own fate. The trunk and the deploy lines differ in one
decisive way — the trunk *populates outward*, a deploy line does not.

<!-- registry:begin -->

| Development | Line          | Carries                                                        | Populates            |
| ----------- | ------------- | -------------------------------------------------------------- | -------------------- |
| core        | `main`        | engine, core, db, console, shared UI — everything every tenant gets | to every `*/main`  |
| sbozh       | `sbozh/main`  | the shell at ludus.sbozh.com — tenant router, one image, no engine | no                   |
| tecraft     | `tecraft/main`| the tecraft tenant at ludus.tecraft.cz — engine + console deploy | no                   |

<!-- registry:end -->

## core — the trunk

`main` is where work goes when **a second tenant would want it**. That is the
whole test. Core work is not "the work sbozh asked for that happens to be
general" — it is work whose home is the trunk from the start, because every
deploy line will eventually receive it.

Populating outward is a decided moment, not a drift: the trunk's work goes
green, a version is declared (`/commit push`), each line receives `main` by
merge and its images are built (`/commit deploy`). Pushing a deploy line and
switching the server stay human acts.

## sbozh — the shell

The tenant router at `ludus.sbozh.com`. Holds no tenant data, ships one image
and no engine, because the shell's whole job is to send `/app/<tenant>/*` to
the right per-tenant project. Work belongs here only when it is the router's
own — its deploy files, its CI, its image.

## tecraft — the first tenant

The tenant at `ludus.tecraft.cz`: engine + console, images tagged
`X.Y.Z-tecraft`, and the production D1 that holds irreplaceable tenant data.
Its line carries only what that deployment needs. Everything about how leads
are actually distributed is **core**, not tecraft's — the tenant is a
deployment, not a feature set.

## Not registered

- **`nsa/main`** — a deploy line registered in the ovidius machinery
  (`OVIDIUS_DEPLOY_LINES`) but deliberately absent from this register.
  Magistratus refuses work on it until it is recorded here. To register it,
  add a row above and a section describing what the line carries.

Registration is a deliberate act: a line that ships without an entry here is a
line nobody agreed to govern.
