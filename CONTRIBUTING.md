# Contributing

## Stage

This repo is at the **proposal / RFC** stage. We are not building a tool yet. The goal is to refine the spec, gather counterexamples, and — if the idea survives scrutiny — encourage someone to pilot it on a real codebase.

## What we want

- **Criticism** — especially "this won't work because…" with a concrete reason.
- **Counterexamples** — existing concepts we missed in [`COMPARISON.md`](./COMPARISON.md).
- **Pilot results** — if you try this on a real codebase, positive *or* negative.
- **Language-specific generators** — a Python/TS/Rust generator that emits the auto-generated fields would be the most valuable contribution.
- **Better sources** — if you have a real benchmark (not a vendor blog) for any claim in [`ESTIMATES.md`](./ESTIMATES.md), please share it.

## What we don't want

- A tool before the spec is stable. Tools lock in decisions.
- Marketing language. This is a proposal, not a product.
- Over-engineering. If a field doesn't directly serve the agent's navigation or verification needs, it probably doesn't belong.

## How to contribute

1. Open an issue first for discussion before opening a PR.
2. PRs that change the spec must bump `spec_version` in [`SPEC.md`](./SPEC.md).
3. Be honest about confidence levels. If you add a claim, label its source.

## Code of conduct

Be honest about what's verified and what isn't. That's the whole code of conduct.
