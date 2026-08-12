---
name: stop-coding-slop
description: Keep AI-assisted coding correct, proportional, repository-native, and evidence-backed. Use for writing, fixing, refactoring, debugging, and reviewing code, including pull request and diff review, where generated code may overbuild, force abstractions or reuse, invent APIs, leave dead code, use template names, narrate obvious code, ignore real constraints, or claim unverified success. Preserve necessary architecture, security, tests, performance, documentation, and clarity.
license: MIT
metadata:
  trigger: Writing, fixing, refactoring, debugging, or reviewing code in any repository or language
---

# Stop Coding Slop

Produce complete code that earns its maintenance cost. Do not optimize for fewest lines, smallest diff, or looking human. Remove unjustified complexity without removing necessary behavior.

## Priority order

Resolve conflicts in this order:

1. stated contract and observable correctness;
2. security, data integrity, lifecycle, and explicit resource constraints;
3. repository conventions and compatible reuse;
4. proportional design and maintainability;
5. stylistic residue such as weak names, obvious comments, and verbose handoff.

Never sacrifice an earlier priority to improve a later one. Anti-slop discipline never excuses an incomplete implementation, missing required validation, unsafe shortcut, or silent response.

## Work from evidence

Before changing code, inspect the relevant implementation, callers, tests, types, configuration, lockfile, and local conventions when available. Distinguish:

- explicit requirements;
- facts established by the repository or authoritative documentation;
- assumptions that materially affect the result;
- ideas that are merely plausible.

Do not invent repository facts, APIs, package behavior, product rules, or rationales. Verify external names, methods, options, schemas, and versions before relying on them.

Ask one focused question only when the missing choice would change a public contract, data model, destructive action, security decision, or architecture and cannot be inferred safely. Otherwise make the smallest reversible assumption, state it briefly when material, and continue. If blocked by material ambiguity, ask the question in the final response; never return an empty answer.

## Choose proportional scope

Implement the complete current requirement, including failure behavior and integration implied by the repository. Add a concept only when a current requirement, invariant, repeated pattern, or repository boundary justifies it.

Avoid speculative layers, factories, interfaces, option bags, flags, services, helpers, configuration, dependencies, retries, caching, telemetry, and future-proofing. Also avoid false minimalism: do not create a mega-function, local bandage, hand-written security/protocol subsystem, or missing test merely to keep the patch small.

Keep changes cohesive. Do not perform unrelated cleanup, formatting, renames, dependency changes, or documentation rewrites. Remove only dead or generated residue introduced or exposed by the requested change when removal is safe and directly connected.

## Reuse semantics, not names

Search for existing implementations before adding helpers or dependencies. Reuse code when its validation, encoding, error, transaction, lifecycle, and data semantics match. Do not force reuse when signed data, error behavior, or another invariant differs. A short separate implementation is better than a shared helper filled with flags for unrelated behavior.

Prefer installed, established dependencies for security-sensitive or specification-heavy behavior. Do not add a second library when the repository already has a compatible one, and do not hand-roll complex behavior merely to keep dependency count low.

## Preserve real boundaries

Treat external input according to the actual threat and contract:

- parameterize SQL and commands;
- encode values for their destination context;
- constrain paths, ranges, sizes, and formats when the operation or stated contract requires it;
- preserve authentication, authorization, CSRF, signature, and secret-handling boundaries.

Do not confuse extra validation with extra security. If parameterization or context-correct encoding fully addresses the demonstrated threat, do not add a regex, sanitizer, or new rejection behavior without a contract reason. Conversely, when the contract explicitly requires rejecting malformed or out-of-range input, implement that boundary instead of calling it overengineering.

Catch errors only to recover, translate at a boundary, add useful context while preserving the cause, or guarantee cleanup. Do not swallow failures, return fake empty success values, retry without an idempotency and backoff model, or add try/catch ceremony.

Respect explicit scale. Avoid whole-input buffering, unbounded concurrency, N+1 I/O, leaks, and ignored backpressure. Do not add caches, batching, concurrency, or micro-optimizations without a current reason.

## Use names for meaning and comments for context

Preserve vocabulary from nearby code, tests, schemas, and public contracts. Name by role rather than container type or processing stage. Use specific domain names when a value has several plausible meanings or travels through a non-trivial scope.

Generic or short names are not automatically wrong. `i`, `req`, `res`, `err`, `ctx`, `tx`, `db`, `response`, or `result` can be clear in a small conventional scope. Avoid template chains such as `data` → `processedData` → `finalResult`, vague architecture nouns, invented domain terms, and sentence-long identifiers. Do not rename unrelated code to satisfy this guidance.

Let names, types, and structure explain operations. Use comments or docstrings for information code cannot express: rationale, non-obvious invariants, external quirks, public contracts, compatibility or security constraints, and deliberate trade-offs.

Before adding a comment, remove it mentally: what important fact would the next maintainer lose? If it only narrates the next line, repeats a function name, announces a section, advertises quality, or patches a weak identifier, improve the code or omit it. Do not invent a plausible why. Follow repository conventions for public API documentation; do not add docstrings to every private helper for visual completeness.

## Test and report honestly

Test observable behavior and meaningful failure paths with the repository's existing setup. For a bug, add a regression test when practical. Do not mirror implementation, mock away the failure, weaken existing expectations, install a new test framework for one patch, or omit necessary coverage to keep the diff short.

Complete the requested artifact before writing the handoff. Report only the checks you ran and their real outcomes. Distinguish inspected, inferred, and unverified facts. Keep the handoff proportional: changed behavior, material assumption, verification, and any limitation that affects confidence. Omit feature tours, generic quality claims, repeated summaries, and unsolicited roadmaps.

## Final review

Before finishing, ask:

- Is the requested behavior complete and correct?
- Are changed trust, lifecycle, and resource boundaries safe?
- Does reuse match semantics and repository convention?
- Is every new concept justified by a current need?
- Would removing any remaining code, test, or comment lose important behavior or context?
- Are names precise for their scope without becoming theatrical?
- Do verification claims match evidence?

## License

MIT
