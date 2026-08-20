# Tools (planned, not built)

This directory is a placeholder for tooling that would support the `COMPONENT.md` spec. Nothing here is built yet. The intent is to specify what tools *should* exist, so that anyone who wants to build one knows the contract.

## Generator

A tool that, given a codebase, emits the auto-generated fields (`location`, `depends_on`, `used_by`) for each `COMPONENT.md`.

- **Input:** a codebase + a list of components (or a heuristic for identifying them).
- **Output:** updated `location`, `depends_on`, `used_by` fields in each existing `COMPONENT.md`. Should not touch authored fields.
- **Language-specific:** each generator targets one language (Python, TypeScript, Rust, etc.). Tree-sitter is the obvious parsing backend.
- **Should run:** in CI on every PR that touches a component file.

## Linter

A tool that validates a `COMPONENT.md` against [`schema/component.schema.json`](../schema/component.schema.json) and the maintenance rules in [`SPEC.md`](../SPEC.md) §6.

Checks:
- All required fields present.
- `location` paths exist on disk.
- `verified_against` is not older than the configured threshold (default: 90 days).
- Authored fields are not empty (a `COMPONENT.md` with empty `change_impact` is a smell).
- No hand-edits to auto-generated fields (detect via git blame or marker comments).

## Freshness checker

A tool that, given a `COMPONENT.md`, reports whether the component file has changed since `verified_against`. Intended for agent consumption — the agent calls this before trusting authored fields.

## Diff helper

A tool that, given a diff to a component file, suggests which authored fields might need updating. Does not edit them — just flags.

## Build status

| Tool | Status |
|---|---|
| Generator (Python) | Not started |
| Generator (TypeScript) | Not started |
| Linter | Not started |
| Freshness checker | Not started |
| Diff helper | Not started |

If you build one of these, please open an issue to coordinate.
