---
title: Tabularium Subsystem
tags:
  - ave
  - kit
  - tabularium
---

# Tabularium Subsystem

The knowledge vault loop — a must-have in the ludus rebuild. This very kit is
written and published through it.

## The shape

An Obsidian-style vault of plain Markdown, kept as **its own git repo**
(submodule in the host project), rendered to a private website by a ready-made
static generator (Quartz), deployed behind Basic Auth. AVE wrote **zero viewer
code** — that is the point. The vault is content; rendering is rented.

## The loop

draft locally → preview (`view`, hot-rebuild) → `commit` → `push` → CI
redeploys the site. Two disciplines make it work:

- **Typed commits, guidance-first.** Vault history uses note-vocabulary types
  (`note:` `edit:` `link:` `move:` `prune:` `meta:`), and the commit verb
  called *without* a message returns the rules + status instead of committing —
  the agent reads, composes, then commits. History stays a readable ledger of
  thought, not `wip` sludge.
- **MCP mirror of the CLI.** Every verb (status/pull/commit/push/view/deploy)
  exists both as a CLI command and an MCP tool, same implementation. An agent
  in a chat ([[brain-connection]]) can draft a note, commit it, and publish —
  which is exactly how Magistratus's written opinions should reach the record.

## Why ludus wants this

Ludus's law practice keeps *binding* text in git (laws as tagged commit
messages) and *working* knowledge in CLAUDE.md/ADRs. The vault adds the third
register: **living notes** — concept docs, rulings' commentary, design
memory — linkable ([[wikilinks]]), graph-viewable, published privately, and
editable by both human and avatar through one audited loop.

## Simpler in the rebuild

- **Vault path is configuration, not convention.** AVE resolves the vault as
  a fixed path against the current working directory (assuming it is the repo
  root), so the CLI fails anywhere else — with a misleading "not installed"
  error rather than naming the real cause. One explicit setting with a
  truthful error beats positional assumptions.
- **Ports are parameters.** The preview server must own *all* its ports
  (Quartz's hot-reload WebSocket defaulted to a port Docker Desktop already
  held — the serve crashed after the build succeeded). Rule: anything that
  listens takes its port from one place, derived defaults included.
- **Keep the commit rules in one module.** They are the vault's constitution;
  CLI, MCP, and any future UI must import the same validator, never restate
  it.
