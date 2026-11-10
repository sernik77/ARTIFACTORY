---
name: artifact-factory
description: Produce, import and maintain knowledge artifacts in ARTIFACTORY. Use when the user asks to research a topic, write up knowledge, docs, a guide, a decision record, a cheatsheet or a prototype, to import a claude.ai artifact or Claude Docs document as Markdown, or to review, refresh or reorganize existing artifacts.
---

# Artifact Factory

You turn questions and material into artifacts that live in `artifacts/<kind>/`. Conventions (kinds, front matter, standards) are in `README.md`; follow them exactly.

## 1. Produce a new artifact

1. **Pick the kind.** A question to answer → `research`. Durable know-how → `knowledge`. A system or tool → `docs`. A task → `guide`. A choice → `decision`. Quick reference → `cheatsheet`. Something runnable → `prototype`.
2. **Check for overlap first:** `bin/af search <key words>`. If an artifact already covers it, update that one instead of creating a near-duplicate.
3. **Scaffold:** `bin/af new <kind> "<Title>" --tags a,b --summary "<one sentence>"`. It prints the path.
4. **Research** when the topic needs facts you cannot verify from the repo: use web search / docs tools, prefer primary sources, and note the access date for anything that will age.
5. **Write** into the template. Delete template guidance you did not use; never leave `...` placeholders. Keep:
   - TL;DR first, answer-shaped.
   - Certainty labels (official / reported / inferred / unknown) on claims that matter.
   - Sources with links.
6. **Finish:** set `summary`, keep `status: draft` unless the user reviewed it, run `bin/af index && bin/af check`, and fix every problem it reports.

## 2. Import a claude.ai artifact or Claude Docs document

1. Read the artifact with the Artifact tool (`action: read`). If it is a Claude Docs doc, read the doc with the Claude Docs connector: `read` the project for its tab and root node id, then `read` the node **with no payload** to get the whole doc as XML.
2. The result is usually too large and is saved to a tool-results file. Pass that file to the importer directly:
   `bin/af import <saved-file> --kind <kind> --origin <artifact URL> --tags a,b --summary "<one sentence>" --status stable`
   (`--created YYYY-MM-DD` keeps the original date.) The importer refuses a read marked `complete: false`.
   If the read came back inline instead, write the XML to a temp file first and import that.
3. Read the top of the output and one table and one code block to confirm the conversion, then `bin/af index && bin/af check`.
4. Never write back to the source artifact or doc; importing is read-only.

## 3. Maintain

- **Revise:** edit the file, bump `updated` to today, re-run `bin/af index && bin/af check`.
- **Review the store:** `bin/af list --status draft` for unfinished work; anything with dated facts older than ~3 months is a candidate for `stale`.
- **Supersede:** set the old artifact to `archived` and add a line at its top linking the replacement.

## Output to the user

Report the artifact path, its one-line summary and whether `bin/af check` passed. Commit only when the user's instructions allow it.
