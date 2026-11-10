# ARTIFACTORY

A git-backed knowledge store. Conventions live in [README.md](README.md); the production workflow lives in the `artifact-factory` skill (`.claude/skills/artifact-factory/SKILL.md`). Load that skill before creating, importing or revising any artifact.

- Artifacts are Markdown in `artifacts/<kind>/`; never hand-edit `CATALOG.md`, regenerate it with `bin/af index`.
- `bin/af` is Python 3 stdlib only. Keep it that way: no third-party packages.
- Artifact content and code are written in English unless the user asks for another language.
- Before committing: `bin/af index && bin/af check` must pass.
