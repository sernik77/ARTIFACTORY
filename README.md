# ARTIFACTORY

The **Artifactory** is a git-backed store of knowledge artifacts: research notes, docs, guides, decisions, cheatsheets and prototypes, all as plain Markdown that GitHub renders.
The **Artifact Factory** is how they get made: templates, the `bin/af` CLI, and a Claude Code skill (`/artifact-factory`) that researches, writes, imports and maintains them.

Browse everything in **[CATALOG.md](CATALOG.md)**.

## Layout

```
artifacts/<kind>/<slug>.md          one artifact = one Markdown file
artifacts/prototype/<slug>/         a prototype = README.md + index.html (+ assets)
templates/<kind>.md                 the skeleton each kind starts from
bin/af                              the factory CLI (Python 3, stdlib only)
CATALOG.md                          generated index: by kind, newest first, plus tags
.claude/skills/artifact-factory/    how Claude produces artifacts here
```

## Kinds

| Kind | Use it for |
|---|---|
| `research` | A question, its sources, findings with confidence, open threads |
| `knowledge` | Distilled, durable know-how: how it works, tips, pitfalls |
| `docs` | Documentation of a system, tool or process |
| `guide` | Step-by-step how-to for one concrete task |
| `decision` | A decision record: context, options, choice, consequences |
| `cheatsheet` | Dense quick reference: commands, defaults, recipes |
| `prototype` | A runnable single-page experiment (no build step) |

## Front matter

Every artifact starts with this block. `bin/af check` enforces it.

```yaml
---
title: "Claude's Token Economy"
kind: knowledge                 # one of the kinds above, must match the folder
status: stable                  # draft | stable | stale | archived
summary: "One sentence on what this artifact answers."
tags: [claude, tokens]
created: 2026-11-10
updated: 2026-11-10
origin: "https://claude.ai/artifact/..."   # optional: where it came from
sources: ["https://..."]                   # optional
---
```

## The CLI

```sh
bin/af new research "How does prompt caching price writes?" --tags claude,caching
bin/af new prototype "Token cost calculator"
bin/af import read-result.txt --kind knowledge --origin https://claude.ai/artifact/...
bin/af index                     # regenerate CATALOG.md
bin/af check                     # validate front matter, links, catalog freshness
bin/af list --kind research --status draft
bin/af search prompt cache
```

`af import` converts a Claude Docs document (the XML a Claude Docs `read` returns, or the saved tool result containing it) into Markdown: headings, nested lists, tables, code blocks, links and inline formatting.
That is how a claude.ai artifact becomes a versioned `.md` here.

## Standards

- **One question per artifact.** If the title needs "and", split it.
- **TL;DR first.** A reader who stops after it should still leave with the answer.
- **Label certainty.** Separate what a source states from what is inferred; date anything that will age.
- **Cite.** Link sources inline or in a Sources section.
- **Tables over prose** when comparing; code blocks for anything copy-pasted.
- **Lifecycle.** New work is `draft`. Promote to `stable` once reviewed. Mark `stale` when facts age, `archived` when superseded (link the replacement).
- Every change: bump `updated`, run `bin/af index && bin/af check`.

CI runs `bin/af check` on every push and pull request.
