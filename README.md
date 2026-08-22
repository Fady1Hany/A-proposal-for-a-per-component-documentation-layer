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


the software engineer asks ai to edit or maintenain COMPONENT_NAME Before ai even go to COMPONENT_NAME to modify it, ai first need to know which COMPONENTS ai are going to check after modifying it. 

## ( _NAME must be unique for each COMPONENT to prevent duplication in COMPONENT_NAME.md files. )

## ( When a user requests a change or maintenance to a component, the prompt must use the exact name defined in the corresponding `COMPONENT_NAME.md` file. The AI will take the provided name and use it as `COMPONENT_NAME.md`, so the name in the prompt must match the component's existing name exactly. )

## ( COMPONENT_NAME =  the component's primary source file. May include multiple files if the component spans them. )

This is the role of "COMPONENT_NAME.md". 

"COMPONENT_NAME.md" gives the AI a list: where is this Component ? , after modifying COMPONENT_NAME, Which COMPONENTS does it need to go through? Which test cases does it need to run?

And there is one important point: before modifying COMPONENT_NAME, the AI must save a copy of the original COMPONENT_NAME source code file.

It needs to do this because if the modification breaks one of the connected components, the AI can restore the original COMPONENT_NAME source code file and try a different implementation of the user's requested change.

And then you need **an orchestration layer** which will tell the ai how to deal with **COMPONENT_NAME.md** .. what the workflow is .. what the end goal is

**AGENTS.md ( or claude code workeflow ) can be used as an orchestration layer**

in the case of using **AGENTS.md** as an orchestration layer it will be like this 

**AGENTS.md :**

Component Change Rule

This rule applies dynamically to any component. Do not assume or hard-code specific component names. The target component must be determined from the user's requested change

For any change to a component — including editing, maintenance, refactoring, bug fixing, or any other modification — do not start by reading or modifying the component's source code.

First, identify the component that the requested change targets.

Then:

1. Locate the documentation file for that component using the following naming convention:"COMPONENT_NAME.md" ( NAME = the actual name of the a component the user asks to change it's code )

2. Read the COMPONENT_NAME.md file before inspecting or modifying its source code.

3. Follow the change and maintenance workflow defined in that documentation file.

4. Use the information in the documentation to understand:
   
   - where the component is located,
   - what the component affects,
   - what affects the component,
   - and any required verification or testing steps.

5. Follow the loop For any change to a component.

So the process becomes a loop:

1. Read "COMPONENT_NAME.md" for COMPONENT_NAME.
   
2. Identify all the COMPONENTs that need to be verified after modifying COMPONENT_NAME.
   
3. Save a copy of the original COMPONENT_NAME code from it's path ( which is documented in COMPONENT_ NAME.md )
   
4. Modify COMPONENT_NAME according to the user's request — for example, adding a new message or changing its behavior.
  
5. Test ( unit test, an integration test, or even an end-to-end (E2E) test.. )
  
6. Continue through the tests listed in "COMPONENT_NAME.md".
  
7. If one of the tests fails, restore the saved original COMPONENT_NAME sorce code. Modify COMPONENT_NAME again using a different approach.
   
8. Run the verification tests again.

9. Repeat this loop until the user's requested change is achieved without affecting any of the connected Components.

___

The important idea is that the AI does not need to keep every previous modification in its memory. It only needs the original saved version of COMPONENT_NAME and the "COMPONENT_NAME.md" file that tells it what to verify.

The AI can therefore iterate repeatedly — even if it tries the same approach many times ( or you can avoid that by saving the approaches outside of the context window for example in database and just query if the current approach was already saved in the database )— until it finds an implementation that satisfies the user's request while keeping all affected services working correctly.   

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
| [`examples/COMPONENT_authentication_service.md`](./examples/COMPONENT_authentication_service.md) | A worked example |
| [`examples/COMPONENT_TEMPLATE.md`](./examples/COMPONENT_TEMPLATE.md) | A blank template to copy |
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
