# ARTIFACTORY
> Centrum dowodzenia artefaktami!
> Artifacts like research notes, documentation, knowledge, prototypes, and more!

### Example artifacts:
- [Claude's Token Economy](https://claude.ai/artifact/NXr6Ujo14614JVHu5KzkYh#0aaadeff-3549.m7eq2ghhfee.37706) (but in .md)

## Architecture

- **Artifactory** — `artifacts/<kind>/<slug>.md`, Markdown with front matter, indexed in [CATALOG.md](CATALOG.md).
- **Artifact Factory** — `templates/`, the `bin/af` CLI (new, import, index, check, list, search) and the Claude Code skill `.claude/skills/artifact-factory`.
- **Import** — claude.ai artifacts and Claude Docs documents convert to `.md` with `bin/af import`.

Details: [README.md](README.md).
