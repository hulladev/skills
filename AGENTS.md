# Repository guidance

This repository distributes consumer skills for the `@hulla/*` package ecosystem.

- Refer to the exact package when known. There is no standalone software product named “Hulla.” Use lowercase `hulla` for the `hulla` CLI.
- Keep skills under `skills/<name>/SKILL.md`, with matching lowercase, hyphenated directory and frontmatter names. Keep metadata values as strings.
- Write for application developers using the packages. Do not apply contributor-only rules, generator commands, or sibling-workspace assumptions to consumer applications.
- Keep essential decisions in `SKILL.md`; add references only for substantial conditional guidance. Link every reference from the entrypoint and copy the entire skill when installing.
- Record package compatibility and reviewed source revisions. Installed exports, declarations, component sources, and registry configuration determine the usable API; online documentation may be newer.
- Prefer focused official Markdown documentation links over duplicated manuals or exhaustive component catalogs. Preserve automatic skill discovery and avoid unnecessary tool restrictions or dependencies.
- Add executable helpers, dependencies, tests, or CI only for a concrete behavioral need. A documentation change alone does not justify adding them.
- Verify skill frontmatter, local reference links, and documented installation in an isolated destination when changing packaging. Do not claim a behavioral agent evaluation unless one was run.
- Preserve unrelated work. Keep changes to sibling package repositories out of scope unless explicitly requested.
