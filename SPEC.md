# SPEC: `COMPONENT.md`

> Status: draft v0.1 — open for discussion

## When a user requests a change or maintenance to a component, the prompt must use the exact name defined in the corresponding `COMPONENT_NAME.md` file. The AI will take the provided name and use it as `COMPONENT_NAME.md`, so the name in the prompt must match the component's existing name exactly.

## ( _NAME must be unique for each COMPONENT to prevent duplication in COMPONENT_NAME.md files. )

## 1. Purpose

`COMPONENT.md` is a small, structured documentation file co-located with a source component, designed to be read by AI coding agents. It gives the agent the minimum information it needs to navigate, predict impact, and verify changes for that component — without reading the whole codebase.

## 2. File placement

- One `COMPONENT.md` per component.
- Placed **in the same directory** as the component's primary source file.
- Named `COMPONENT.md` (uppercase, to distinguish from human-oriented `.md` files).
- A component is defined as "a unit of code with a public interface that other components depend on" — typically a class, a module, or a small cohesive group of files.

Try to make the definition of a component as small as possible.

Why? For example, if you define a component as an entire microservice, and you change a function inside that microservice, that function may be connected to several other functions. The AI may not know that those relationships exist because the "COMPONENT.md" file is defined at the microservice level rather than at the function level. As a result, it will not be able to test the change properly.

So, as much as possible, make the definition of a component as small and precise as possible. This allows the AI to identify and test all the relevant relationships whenever a change is made, and therefore verify whether the change actually works without breaking anything else.

You also should not worry too much about having a large number of component files or about the fact that a smaller component may have many connected components. That is not a problem because the AI does not need to know the code of every component connected to the component it is modifying.

It only needs to know the code of the component it is actually modifying.

The other components connected to it can be treated as black boxes. The AI does not need to understand their internal implementation. It only needs to run tests and wait for the expected results.

For example if you use **AGENTS.md** as a orchestration layer what needs to be present in the AI's context window is AGENTS.md ( which contains many instructions one of them is the loop which is described in [Readme](https://github.com/Fady1Hany/A-proposal-for-a-per-component-documentation-layer/blob/main/README.md) ) , COMPONENT_NAME.md , the code of the component it is currently modifying, and the saved path or copy of that component's code from before the modification.

This keeps the amount of code the AI needs to understand small, while still allowing it to verify the impact of its changes across the connected components.


## 3. Required fields

Every `COMPONENT.md` MUST contain the following fields, in this order:

### 3.1 `component`
The component's name as it appears in code.

### 3.2 `location`
The exact file path (relative to repo root) of the component's primary source file. May include multiple files if the component spans them.

### 3.3 `verified_against`
The git commit hash (or short hash) at which this document was last verified to be accurate. **This field is the freshness contract.** An AI agent SHOULD check this against the current state of the file before trusting the authored fields (Change impact, Targeted verification). If the component has changed since `verified_against`, the agent SHOULD treat authored fields as stale and re-derive them from source.

### 3.4 `depends_on`
Components this component calls or imports. Each entry includes the dependency's name and its file location. **Auto-generated** by a tool; not hand-authored.

### 3.5 `used_by`
Components that call or import this component. Each entry includes the consumer's name and its file location. **Auto-generated** by a tool; not hand-authored.

### 3.6 `change_impact`
Authored. Describes what kinds of changes to this component affect which downstream components, and how. Examples:
- "Changing the signature of `validate(token)` requires updates to `LoginController` and `SessionManager`."
- "Changes to token validation logic may affect `SessionManager` session creation."

### 3.7 `targeted_verification`
Authored. Lists the specific tests or checks to run after modifying this component. Prefer exact test names. Examples:
- `test_login_flow`
- `test_token_validation`
- `test_session_creation`

## 4. Optional fields

- `public_interface` — explicit list of exported symbols considered stable. Changes to these are breaking.
- `stability` — one of `experimental`, `stable`, `frozen`, `deprecated`.
- `notes` — free-form authored context that does not fit elsewhere. Use sparingly.

## 5. Format

- Markdown, with a fixed field layout (see template).
- A JSON Schema is provided in [`schema/component.schema.json`](./schema/component.schema.json) for tooling.
- The on-disk format is Markdown-first; tooling may parse it into structured form.

## 6. Maintenance rules

1. **Auto-generated fields** (`location`, `depends_on`, `used_by`) are regenerated by a tool on every commit or PR. Hand-editing them is forbidden.
2. **Authored fields** (`change_impact`, `targeted_verification`, `notes`) are maintained by humans.
3. **`verified_against`** is updated whenever a human reviews and confirms the authored fields are still accurate. A CI check SHOULD fail if `verified_against` is older than a configurable threshold (default: 90 days).
4. A CI check SHOULD fail if `location` no longer exists on disk (file moved or deleted without updating the doc).

## 7. Agent consumption contract

An AI agent reading a `COMPONENT.md` SHOULD:

1. Read `verified_against` first.
2. Compare the component file's current commit to `verified_against`.
3. If stale, treat `change_impact` and `targeted_verification` as low-confidence and re-derive from source.
4. Always trust `location`, `depends_on`, `used_by` (they are auto-generated and fresh).
5. Use `targeted_verification` to select which tests to run, not as a substitute for the agent's own judgment.

## 8. What is intentionally not in the spec

- A specific generator implementation. Any tool that emits the required fields in the right format is fine.
- A specific language. The format is language-agnostic; generators will be language-specific.
- A specific test framework. `targeted_verification` is just a list of identifiers.

## 9. Versioning

This spec is `v0.1`. Breaking changes will increment the major version. A `spec_version` field MAY be added to the file if backwards compatibility becomes a concern.
