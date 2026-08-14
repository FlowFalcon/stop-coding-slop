# Stop Coding Slop

An Agent Skill for producing focused, repository-native AI-assisted code.

[English](README.md) · [Bahasa Indonesia](README_id.md)

`stop-coding-slop` makes a coding agent slow down before editing, define what success means, choose the simplest complete solution, keep the diff surgical, remove generated residue, and report only verification that actually happened.

It does not try to disguise AI authorship. It improves the engineering quality of AI-assisted changes.

## What is AI coding slop?

AI coding slop is code, tests, comments, or handoff prose that looks plausible at first glance but is weakly grounded in the repository and creates avoidable maintenance or review work.

The problem is not that an LLM wrote the code. AI-generated code can be correct, direct, and maintainable. Slop appears when the model optimizes for producing a convincing completion instead of integrating the requested behavior into the actual system.

A slop-heavy patch often has one or more of these properties:

- it starts coding before understanding the existing behavior;
- it invents imports, APIs, framework conventions, or repository facts;
- it creates abstractions for hypothetical future requirements;
- it changes unrelated files because they are nearby;
- it adds defensive branches, retries, fallbacks, or configuration with no current contract;
- it duplicates existing behavior or forces reuse where semantics differ;
- it narrates obvious code with comments;
- it leaves placeholders, dead branches, examples, or alternative implementations behind;
- it handles the happy path while missing authorization, transactions, cleanup, scale, or failure behavior;
- it writes tests that mirror implementation details without proving the required behavior;
- it claims tests passed even though no test command was run.

The code may compile. Some of it may even work. It is still slop when a maintainer must remove speculative machinery, rediscover the real requirement, repair boundary behavior, or verify unsupported claims before the change can be trusted.

## Small signals that add up

| Signal | What it looks like | Why it costs the repository |
|---|---|---|
| First-answer coding | The first plausible implementation is emitted immediately | Existing helpers, callers, failure paths, and ownership are missed |
| Guessed integration | Placeholder imports, invented request fields, assumed transaction APIs | The patch may not compile or may silently use the wrong boundary |
| Premature abstraction | A service, factory, strategy, interface, or option bag for one use case | Review surface and future maintenance grow without current value |
| False minimalism | A tiny patch that omits validation, rollback, cleanup, or tests | Fewer lines hide an incomplete contract |
| Broad cleanup | Renames, formatting, dependency changes, or refactors mixed into the task | Reviewers cannot isolate the behavioral change |
| Forced reuse | Two paths share a helper despite different validation, errors, side effects, or lifecycle | Reuse hides semantic differences and creates flags or branching |
| Defensive noise | Unrequested retries, fallbacks, regex validation, telemetry, or compatibility shims | New behavior and failure modes appear without requirements |
| Narration comments | `// Validate the input` directly above an obvious validation check | Comments repeat syntax instead of preserving knowledge |
| Template naming | `data`, `item`, `processData`, `BaseManager`, `ResultHandler` | Domain meaning disappears behind generic vocabulary |
| Boundary blindness | Whole-file buffering, unbounded concurrency, swallowed cancellation, byte/character confusion | The implementation works in demos but fails under real constraints |
| Shallow tests | Tests assert only a final row count when ordering or atomicity is the contract | A green test does not prove the important invariant |
| Confident handoff | “All tests pass” without command output or repository evidence | Reviewers receive false confidence instead of verification status |

No single pattern proves that a patch is bad. The skill evaluates each addition against the current contract and repository evidence rather than using a stylistic detector.

## Why coding agents produce it

Coding agents are often asked to act with incomplete context. They can fill missing information with common patterns, continue beyond the requested scope, or produce explanatory material because those outputs look complete in isolation. Repository work is different: correctness depends on local contracts, existing abstractions, operational limits, and failure behavior that cannot be inferred safely from a generic example.

The remedy is not “write fewer lines” or “sound more human.” The remedy is to inspect before editing, make uncertainty explicit, and connect every added concept to a current requirement.

## The four operating principles

### 1. Think before coding

Do not translate the request directly into the first plausible patch. Inspect the implementation, callers, tests, types, configuration, and nearby conventions. Search for existing behavior before adding new behavior.

This is a short evidence-gathering step, not an excuse for analysis paralysis. Once the available evidence defines a safe contract, implement it.

### 2. Define clear success criteria

Before choosing code, identify:

- observable success behavior;
- meaningful failure behavior;
- authorization, compatibility, scale, transaction, and lifecycle constraints;
- the tests or checks that will demonstrate success;
- the smallest set of files that owns the behavior.

If success cannot be described in observable terms, the implementation target is still unclear.

### 3. Prefer simplicity

Choose the most direct design that satisfies the complete current contract. Simplicity means fewer concepts and less indirection, not blindly minimizing line count.

A correct direct loop can be simpler than a reusable pipeline. A required transaction, cleanup path, or regression test is not overengineering. An abstraction justified only by a possible future feature is.

### 4. Make surgical changes

Change only the files and lines needed for the contract. Preserve public names and repository structure unless the request requires otherwise. Every expansion in scope should have a concrete reason tied to current behavior.

Surgical does not mean incomplete. If a route, domain function, and regression test all own parts of the behavior, changing all three is still a focused patch.

## How the skill works

The instructions in [SKILL.md](SKILL.md) guide an agent through this sequence:

1. Inspect repository evidence before editing.
2. Form a compact internal change contract.
3. Select the simplest complete implementation shape.
4. Keep the edit within the files that own the behavior.
5. Produce the requested artifact instead of stopping at a plan.
6. Remove speculative helpers, branches, comments, placeholders, and prose.
7. Run existing checks and report their real results.

The skill also protects against false simplicity. It explicitly preserves required authorization, validation, transactions, cleanup, backpressure, tests, and error propagation.

## A small example

Suppose a task asks for a bulk preference endpoint that must parse every item before writing and commit all updates atomically.

A slop-prone response may:

- create a new `PreferenceUpdateService`, schema layer, retry policy, and configuration object;
- parse and write each item in the same loop, leaving partial data when a later item is invalid;
- add comments describing each obvious statement;
- test only the `204` response;
- finish with “all tests pass” without running tests.

A repository-native response instead:

- reuses the existing parser, repository, audit helper, and transaction boundary;
- parses the complete input before the first write;
- performs ordered writes inside one transaction;
- adds tests for success, invalid size, invalid-entry atomicity, and authentication;
- reports exactly which checks ran, or says they were not executed.

The better patch may not be the shortest patch. It is the smallest patch that proves the whole behavior.

## What this skill does not do

- It does not make generated code intentionally look human-authored.
- It does not ban abstractions, comments, validation, or defensive code when the contract requires them.
- It does not replace repository tests, static analysis, security review, or maintainer judgment.
- It does not encourage clever compression or code golf.
- It does not silently change public APIs or architecture to make a diff smaller.
- It does not turn missing evidence into guessed implementation details.

## When to use it

Use the skill when:

- implementing a feature or bug fix with an AI coding agent;
- reviewing or cleaning an AI-assisted diff;
- refactoring code that accumulated speculative layers or generated residue;
- a patch contains generic helpers, duplicated logic, broad cleanup, or narration comments;
- a handoff makes verification claims that need to be checked;
- you want a final repository-native quality pass before review.

## Installation

Clone the repository:

```bash
git clone https://github.com/FlowFalcon/stop-coding-slop.git
```

### Claude Code

Copy the folder into a personal or project skill directory:

```bash
cp -R stop-coding-slop ~/.claude/skills/stop-coding-slop
```

For a project-local installation, use `.claude/skills/stop-coding-slop`.

### Codex

Copy the folder into the configured Codex skills directory:

```bash
cp -R stop-coding-slop ~/.codex/skills/stop-coding-slop
```

### Other agents

Any agent that supports the Agent Skills convention can load [SKILL.md](SKILL.md). For agents without skill discovery, include its contents as task context or a system instruction.

## Usage

Invoke the skill explicitly when your agent supports named skills:

```text
Use $stop-coding-slop to implement this change. Inspect the repository first,
define observable success, and return a focused diff with honest verification.
```

It can also be used as a final pass:

```text
Use $stop-coding-slop to review this diff. Remove generated residue without
changing required behavior, then report the checks that actually ran.
```

## Evaluation

The bundled [eval suite](evals/evals.json) compares behavior with and without the skill across three long-form TypeScript tasks:

- an atomic bulk preference endpoint;
- a bounded streaming event importer;
- a transactional order cancellation flow.

Each task has six assertions covering correctness, failure behavior, transaction or scale constraints, regression tests, patch scope, generated residue, and handoff honesty. The suite is designed to expose differences that are difficult to see in tiny code-generation prompts.

## Repository structure

```text
stop-coding-slop/
├── agents/openai.yaml   # Agent-facing UI metadata
├── evals/evals.json     # Long-form with/without-skill evaluations
├── SKILL.md             # Instructions loaded by the coding agent
├── README.md            # English documentation
├── README_id.md         # Indonesian documentation
├── CHANGELOG.md
└── LICENSE
```

## Design rule

The goal is not code that merely avoids obvious AI style. The goal is a change a maintainer can understand, verify, debug, and extend because it follows repository evidence and implements a clear contract.

## Author

FlowFalcon (Fathur)

## License

MIT. See [LICENSE](LICENSE).
