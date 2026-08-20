# AI-Oriented Component Documentation

> A proposal for a per-component documentation layer designed around the context-window constraint of AI coding agents.

## The problem

An AI coding agent does not have a persistent mental model of your codebase. Every interaction starts from near-zero: it must rediscover where things are, what calls what, what breaks if it changes a given file, and which tests to run afterward. As a codebase grows, this rediscovery becomes expensive — in context budget, in time, and in reliability.

A human developer who wrote the code carries most of this implicitly. The AI does not.

## The proposal

For every important component in a codebase, co-locate a small, structured documentation file that tells an AI agent:

1. **Location** — where the component lives
2. **Relationships** — what calls it, what it calls, what depends on it
3. **Change impact** — what may break if this component changes, and how
4. **Targeted verification** — which specific tests/checks to run after modifying it

The goal is not to replace source inspection. It is to give the agent a high-level navigation and impact map *first*, so it can identify the relevant slice of the codebase before spending context on the implementation.

## What makes this different from existing things

This is not AGENTS.md (one file per repo, behavioral rules). It is not a code-graph MCP server (auto-generated, queried at runtime). It is not an ADR (per-decision, not per-component).

The closest existing concept is **AI-Readable Architecture** (Shumilov, 2026), which makes a similar argument at the architecture level. This proposal narrows that idea to a concrete per-component schema, with one key addition: an explicit split between **auto-generated structural fields** (Location, Relationships) and **authored intent fields** (Change impact, Targeted verification), plus a `verified_against` field that lets the agent check doc freshness before trusting it.

See [`COMPARISON.md`](./COMPARISON.md) for the full landscape.

## What's in this repo

| File | Purpose |
|---|---|
| [`SPEC.md`](./SPEC.md) | The formal spec for the `COMPONENT.md` format |
| [`RATIONALE.md`](./RATIONALE.md) | Why this exists, the problem it solves |
| [`COMPARISON.md`](./COMPARISON.md) | How it differs from AGENTS.md, Aider repo maps, MCP code-graph servers, ADRs, etc. |
| [`ESTIMATES.md`](./ESTIMATES.md) | Realistic estimates of where this helps, with confidence levels |
| [`SOURCES.md`](./SOURCES.md) | Honest audit of the evidence base — what's verified, what's vendor-cited, what's reasoning |
| [`examples/authentication_service.COMPONENT.md`](./examples/authentication_service.COMPONENT.md) | A worked example |
| [`examples/TEMPLATE.component.md`](./examples/TEMPLATE.component.md) | A blank template to copy |
| [`schema/component.schema.json`](./schema/component.schema.json) | JSON Schema for the format |
| [`CONTRIBUTING.md`](./CONTRIBUTING.md) | How to contribute / discuss |

## Status

**Stage: proposal / RFC.** Not a tool. Not a standard. Not validated on a real codebase yet.

The intent of publishing this repo is to invite discussion, gather counterexamples, and — if the idea survives scrutiny — encourage someone to pilot it on a real 500+ component codebase and report what actually happens.

## License

MIT. See [`LICENSE`](./LICENSE).

## Contributing

Open an issue with criticism, counterexamples, or pilot results. Pull requests that improve the spec or add language-specific examples are welcome. See [`CONTRIBUTING.md`](./CONTRIBUTING.md).
