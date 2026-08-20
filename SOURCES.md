# Sources — honest audit

> This document exists because several claims in this repo are weaker than they first appeared. The intent is to be transparent about what's verified, what's vendor-cited, and what's reasoning. If you find a claim here that's overstated, please open an issue.

## The four sources cited in the original analysis

### 1. The arXiv paper on AGENTS.md

- **Title:** *"Evaluating AGENTS.md: Are Repository-Level Context Files Actually Useful?"*
- **Date:** February 2026.
- **Source:** arXiv:2602.11988v1
- **What it actually says (per abstract):** Across multiple coding agents and LLMs, repository-level context files tend to reduce task success rates compared to providing no repository context at all.
- **How we used it:** As a cautionary data point that *poorly designed* agent context can hurt.
- **Honest caveat:** This paper is about **project-level context files** (one AGENTS.md per repo, behavioral rules). It is *not* a direct test of per-component structural documentation. The analogy is indirect: same broad mechanism (extra context), different specific artifact. It does *not* prove that well-designed per-component context will hurt — but it is a warning.

### 2. The "30% increase in change-failure rates" claim

- **Source cited:** Reddit post linking to riftmap.dev blog.
- **What the blog says:** Cites "2026 data — Cortex, DORA, Amazon's memo" to claim AI coding agents have pushed change-failure rates up ~30% on cross-file changes.
- **Honest problem:** This is a **vendor blog** (riftmap sells blast-radius tooling) citing secondary sources we have not read. The specific "30%" figure is vendor-cited and should be treated as directional, not quantified.
- **Defensible version of the claim:** "AI coding agents have measurably higher failure rates on cross-file changes than on localized changes" — that part is plausible and consistent with how the tools behave. The specific magnitude is uncertain.
- **Status:** **Low confidence.** Used as a directional indicator of problem size, not as a precise number.

### 3. Cursor / Aider public claims about codebase indexing

- **Sources:**
  - Aider documentation on its PageRank-based repo map (aider.chat/docs/repomap.html).
  - Cursor marketing on semantic indexing.
  - Codegen.com definition of codebase indexing.
- **Honest problem:** These are **descriptions of mechanisms** and **marketing claims**, not benchmarked outcome studies. We conflated "they describe how it works" with "they have evidence it works."
- **What they actually support:** The *existence* of the mechanism (auto-generated code graphs fed to agents). Not the *efficacy* of the mechanism.
- **Status:** **Weak.** Listed as evidence for the mechanism existing, not for it being effective.

### 4. First-principles reasoning

- **The argument:**
  - An agent has a context budget of roughly 100k–1M tokens depending on the model.
  - Each file Read costs ~1–4k tokens.
  - On a 500-component codebase, locating and reading the relevant slice without a map costs ~10–20 tool calls (~20–80k tokens).
  - A per-component doc compresses the navigation step to ~1 Read (~500 tokens).
  - Therefore navigation cost is reduced by roughly the ratio of those two numbers.
- **Status:** **High confidence in the upper bound.** This is arithmetic, not a citation.
- **Caveat:** This gives the *theoretical* saving if the doc is perfect. Real savings are lower because (a) agents don't always need 20 calls, (b) the doc isn't always perfect, (c) sometimes the agent still has to read the actual source.

## Related concepts (verified to exist)

| Concept | Source | Status |
|---|---|---|
| AGENTS.md convention | agents.md, GitHub `agentsmd/agents.md` | Verified — open standard |
| Aider repo map | aider.chat/docs/repomap.html | Verified — well-documented mechanism |
| AI-Readable Architecture (Shumilov) | *Contemporary*, May 2026; ResearchGate publication 405269777 | Verified — peer-reviewed concept, closest match |
| CodeGraph MCP server | github.com/colbymchenry/codegraph, mcpservers.org | Verified — exists |
| Depwire MCP server | news.ycombinator.com/item?id=47169193 | Verified — exists |
| Software Change Impact Analysis (Lehnert) | d-nb.info review, cited 243+ | Verified — established research field |
| Architectural Decision Records | adr.github.io | Verified — established practice |
| Coderabbit Security Blast Radius | docs.coderabbit.ai | Verified — exists, narrower scope |

## Confidence summary

| Claim | Confidence | Reason |
|---|---|---|
| The four pieces of the proposal all exist in some form | High | Verified across multiple sources |
| The exact combination does not exist as a named concept | Medium-high | Negative result from search; could exist under a name we didn't find |
| Cross-file edit failure rate is higher than localized | High (direction), Low (magnitude) | Direction is consistent across sources; "30%" is vendor-cited |
| Navigation cost is reducible by compressing to a per-component doc | High (upper bound) | Arithmetic |
| Authored docs drift faster than auto-generated graphs | High | Well-known phenomenon |
| AI-Readable Architecture is the closest academic match | High | Verified |

## What we did not verify

- We did not read the underlying Cortex, DORA, or Amazon sources that riftmap.dev cites.
- We did not run any benchmarks on the proposed format — none exist.
- We did not validate the `ESTIMATES.md` numbers against a real pilot — no pilot has been run.
- We did not exhaustively search for the concept under every possible name. There may be prior art we missed.

## How to cite this repo

If you build on this proposal, cite the repo directly. Do not cite the estimates as if they were benchmarks. If you run a pilot and get different numbers, please publish them and open an issue — negative results are especially welcome.
