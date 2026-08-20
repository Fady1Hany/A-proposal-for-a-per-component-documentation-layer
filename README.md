# AI-Oriented Component Documentation

> A proposal for a per-component documentation layer designed around the context-window constraint of AI coding agents.AI was used as a research and writing assistant in developing this proposal. The underlying idea, core concepts, and direction of the proposal are my own.


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


the software engineer ask ai to edit or maintenance  Component X Before ai even go to Component X to modify it, ai first need to know which COMPONENTS ai are going to check after modifying it.

This is the role of "COMPONENT X.md". 

"COMPONENT X.md" gives the AI its to-do list: after modifying Component X, exactly what does it need to do? Which COMPONENTS does it need to go through? Which test cases does it need to run?

And there is one important point: before modifying Component X, the AI must save a copy of the original Component X file.

It needs to do this because if the modification breaks one of the connected services, the AI can restore the original Component X file and try a different implementation of the user's requested change.

So the process becomes a loop:

1. Read "COMPONENT X.md" for Component X.
2. Identify all the COMPONENTs that need to be verified after modifying Component X.
3. Save a copy of the original Component X code.
4. Modify Component X according to the user's request — for example, adding a new message or changing its behavior.
5. Test the next connected service.
6. Continue through the Components listed in "COMPONENT X.md".
7. If one of the tests fails, restore the saved original Component X code. Modify Component X again using a different approach.
9. Run the verification tests again.
10. Repeat this loop until the user's requested change is achieved without affecting any of the connected Components.

The important idea is that the AI does not need to keep every previous modification in its memory. It only needs the original saved version of COMPONENT X and the "COMPONENT X.md" file that tells it what to verify.

The AI can therefore iterate repeatedly — even if it tries the same approach many times ( or you can avoid that by save the approaches outside of the context widow for example in database and just query if the current approach was already saved this database )— until it finds an implementation that satisfies the user's request while keeping all affected services working correctly.   

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
| [`tools`](./tools) | all tools that are created to auto-generate the fields that can be auto generated will live under (./tools)|

## Status

**Stage: proposal / RFC.** Not a tool. Not a standard. Not validated on a real codebase yet.

The intent of publishing this repo is to invite discussion, gather counterexamples, and — if the idea survives scrutiny — encourage someone to pilot it on a real 500+ component codebase and report what actually happens.

## License

MIT. See [`LICENSE`](./LICENSE).

## Contributing

Open an issue with criticism, counterexamples, or pilot results. Pull requests that improve the spec or add language-specific examples are welcome. See [`CONTRIBUTING.md`](./CONTRIBUTING.md).
