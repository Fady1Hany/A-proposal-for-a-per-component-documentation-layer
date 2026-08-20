# Estimates of improvement

> Honest assessment of where this proposal helps, where it doesn't, and how confident we are in each claim. **No one has measured this exact concept.** The numbers below are reasoned estimates, not benchmarks.

## How to read this document

Each estimate has a **confidence level**:

- **High** — supported by arithmetic or directly observable behavior
- **Medium** — direction is well-supported, magnitude is a reasoned guess
- **Low** — single source, vendor-cited, or composite of weaker claims

Treat all numbers as **hypotheses to measure on your own codebase**, not as predictions.

## Where the gains are large

### 1. Cross-file refactors and changes with downstream consumers
- **Estimated improvement:** failed/broken-edit rate drops 50–80% on changes with ≥3 downstream consumers.
- **Confidence:** Medium.
- **Reasoning:** Today, agents typically read the file they're modifying and stop. Discovering downstream consumers requires multiple grep rounds that eat context. With the `used_by` and `change_impact` fields, the agent knows up front which files to touch. The direction is sound; the magnitude is a guess.
- **Baseline problem:** AI coding agents have measurably higher failure rates on cross-file changes than on localized changes. Vendor-cited data (riftmap.dev, citing DORA/Cortex) puts the gap around 30%, but see [`SOURCES.md`](./SOURCES.md) for caveats.

### 2. Navigation cost on large codebases
- **Estimated improvement:** context budget saved on navigation is 40–70% for codebases above ~200 components.
- **Confidence:** Medium-high.
- **Reasoning:** This is mostly arithmetic. An agent has ~100k–1M tokens of context. Each file Read costs ~1–4k tokens. On a 500-component codebase, locating the right slice without a map costs ~10–20 tool calls (~20–80k tokens). A per-component doc collapses that to ~1 Read (~500 tokens). The upper bound is grounded in arithmetic; real savings are lower because agents don't always need 20 calls and the doc isn't always perfect.

### 3. Test selection
- **Estimated improvement:** verification loop time drops 60–90% for changes where the doc's `targeted_verification` list is accurate.
- **Confidence:** Medium.
- **Reasoning:** Most agents today either run all tests (slow, expensive) or guess based on file proximity (often wrong). Targeted verification lets the agent run the 3 tests that actually matter. True *if* the test list is accurate; depends entirely on doc quality.

## Where the gains are small or zero

### 1. Single-file, self-contained changes
- **Estimated improvement:** 0–20%. Possibly net-negative due to doc-reading overhead.
- **Confidence:** High (by definition — no cross-component surface to map).
- **Reason:** If the change doesn't touch anything else, the doc adds overhead for zero benefit. Probably 30–50% of day-to-day edits fall here.

### 2. Small codebases (<50 components)
- **Estimated improvement:** negligible.
- **Confidence:** High.
- **Reason:** The agent can grep the whole thing in one pass. The map's value is marginal.

### 3. Well-typed, well-named code
- **Estimated improvement:** smaller than for dynamic or legacy code.
- **Confidence:** Medium.
- **Reason:** Strong type systems (TypeScript, Rust, Haskell) already encode a lot of the `depends_on`/`used_by` information for free. The structural fields are lower-value because the agent can derive them from types.

### 4. Greenfield work
- **Estimated improvement:** near zero.
- **Confidence:** High.
- **Reason:** No history, no implicit knowledge to surface. The doc is mostly empty.

## Where it can actively hurt

### Stale docs
- **Risk:** High, if not mitigated.
- **Mitigations in this spec:** `verified_against` freshness field; auto-generated structural fields; CI checks for staleness and missing files.
- **Evidence:** The arXiv paper on AGENTS.md found context files can *reduce* task success rates. Bad context is worse than no context.

### Wrong authored fields
- **Risk:** Medium.
- **Reason:** If `change_impact` says "this won't affect X" when it actually does, the agent will trust that and propagate the wrongness.
- **Mitigation:** Treat authored fields as low-confidence when `verified_against` is stale.

### Over-trust
- **Risk:** Medium.
- **Reason:** Agents tend to treat documented facts as ground truth. A confident doc that's slightly wrong is more dangerous than no doc at all.

## Bottom line

For a **mature, 500-component codebase with cross-cutting changes**, the realistic upside is:

| Metric | Estimate | Confidence |
|---|---|---|
| Cross-file edit success rate | ~70% → ~90%+ | Medium |
| Context budget per navigation-heavy task | −30 to −50% | Medium-high |
| Verification loop time | −60 to −90% | Medium |
| Overall AI-assisted maintenance productivity | +20 to +40% | Low-medium |

For a **small or clean codebase**, expect single-digit improvements at best, and possible net-negative if the doc overhead exceeds the gains.

## What this is not

- **Not a multiplier on AI intelligence.** The agent does not get smarter. It stops making the same two classes of mistakes: missed downstream consumers and wrong test selection.
- **Not a substitute for source inspection.** The agent should still read the actual code before editing.
- **Not a benchmark.** These are hypotheses. Anyone piloting this should instrument before/after metrics on their own codebase.

## Recommended pilot

If you want to validate these numbers:

1. Pick a real 200+ component codebase.
2. Add `COMPONENT.md` to the 20–30 most-changed components (not all of them).
3. Measure for 4–8 weeks: cross-file edit success rate, context budget per task, verification loop time.
4. Compare to a control period on the same codebase.
5. Publish the results, positive or negative.
