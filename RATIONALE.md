# Rationale

## The asymmetry between human and AI developers

A human developer who has worked on a codebase for any length of time carries a large amount of implicit knowledge: where the important components live, which parts call which, which tests cover what, and which changes are likely to ripple. When asked to modify a component, they often already know where it is and what it touches — or they can navigate there in a few keystrokes.

This is not because humans have larger working memory than AI agents. It is because humans build a **persistent mental model** that survives across interactions. They can selectively recall the parts that matter without reloading the whole project.

An AI coding agent does not have this. Each interaction starts with a near-empty context window. The agent must rediscover the structure of the codebase through tools — file search, code search, dependency tracing, reading source — every time. As the codebase grows, the cost of this rediscovery grows with it.

## Why existing approaches do not fully solve this

- **Human-oriented documentation** (READMEs, ADRs, Javadoc) is written for a reader who already has the implicit context. It describes *what* something does, rarely *where it sits in the dependency graph* or *what breaks if it changes*.
- **Auto-generated code graphs** (Aider repo map, Cursor indexing, CodeGraph MCP) extract structure on demand, but they cost context budget on every query and they do not capture *intent* — which test is the canonical regression check, which interface is stable, which consumer is known-fragile.
- **Project-level context files** (AGENTS.md) give the agent behavioral rules and conventions for the whole repo, but they are one file, not a per-component map.

The gap is: there is no convention for **a per-component documentation artifact that explicitly tells an AI agent where the component is, what it interacts with, what may break if it changes, and which checks to run afterward**.

## The design constraint

This proposal is designed around a single constraint that is fundamental to current AI agents:

> The agent has a limited active context window. It cannot hold the entire codebase, all relationships, and all relevant implementation details at once.

Everything in the spec follows from this. The four fields (Location, Relationships, Change impact, Targeted verification) are chosen because they are the minimum information an agent needs to:

1. Decide whether to engage with this component at all
2. Identify the relevant slice of the codebase before reading source
3. Predict the blast radius of a change
4. Verify the change without running the entire test suite

## What this is not

- It is not a replacement for source inspection. The agent should still read the actual code before editing.
- It is not a general-purpose documentation format. It is specifically optimized for AI consumption.
- It is not a tool. It is a convention. Tools (generators, linters, CI checks) are encouraged but not specified here.

## Why now

The shift from "AI as autocomplete" to "AI as agent that edits across multiple files" is what makes this worth proposing. When an agent only completed the next line, it did not need an impact map. When an agent modifies a service and is expected to update its consumers and run the right tests, the absence of an impact map becomes the dominant failure mode.

The arXiv paper *"Evaluating AGENTS.md"* (Feb 2026) found that repository-level context files can *reduce* task success rates — which is a warning that **design matters more than existence**. This proposal takes that warning seriously: the format is small, co-located, freshness-stamped, and split between auto-generated and authored fields to minimize drift.
