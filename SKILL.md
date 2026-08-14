---
name: stop-coding-slop
description: Remove AI-generated coding residue by defining success before editing, inspecting repository evidence, choosing the simplest complete solution, keeping changes surgical, and verifying observable behavior. Use when the user asks for repository-native, non-sloppy, minimal, maintainable code; when implementing, debugging, refactoring, or reviewing a change that may contain speculative abstractions, generic naming, redundant comments, duplicated logic, defensive branches, broad cleanup, invented APIs, or unverified claims; or as a final quality pass on an AI-assisted diff in an existing repository.
---

# Stop Coding Slop

Make the change native to the repository because it follows repository evidence—not because it imitates a human style. Optimize for the maintainer who must review, debug, and extend it.

## Think before coding

Do not translate the request directly into the first plausible patch. Inspect the relevant implementation, callers, tests, types, configuration, and nearby conventions before editing. Search for existing behavior before adding it. Trace the path far enough to understand where the behavior belongs and what could break.

## Define success before implementation

Form a short internal change contract before choosing code. Establish:

- required observable behavior and meaningful failure behavior;
- trust, compatibility, scale, and lifecycle constraints;
- the checks or evidence that will demonstrate success;
- the smallest set of files that owns the behavior.

If success cannot yet be stated in observable terms, keep investigating. Do not use implementation activity to discover the requirement by accident. Keep this reasoning proportional and mostly internal: once the available evidence defines a safe contract, implement it instead of returning a long plan or waiting for unnecessary certainty.

Treat everything else as a hypothesis. Do not invent repository facts, APIs, import paths, framework types, package behavior, or rationales. If a path or type is missing, keep the provided symbol in scope or state the limitation; never emit a guessed or placeholder import. Ask only when ambiguity changes a public contract, data model, destructive action, security decision, or architecture.

## Prefer the simplest complete shape

Choose the most direct design that satisfies the whole current contract. Simplicity means fewer concepts and less indirection, not merely fewer lines. Add no concept justified only by possible future use.

- Reuse code only when validation, output transformation, errors, side effects, transaction, and lifecycle semantics match.
- Keep distinct semantics separate. A few direct lines are cheaper than a shared helper with flags or hidden behavior changes.
- Add an abstraction only for a current boundary or repeated variation.
- Preserve public names and shapes unless the task requires changing them. Improve local names without creating an API migration.

Do not optimize for fewest lines. Preserve required authorization, validation, transactions, cleanup, backpressure, tests, and error propagation. Validate at the actual boundary; do not add regexes, fallback values, retries, or rejection behavior unrelated to the contract.

## Keep changes surgical

Change only the files and lines needed to satisfy the contract. Follow the existing ownership and structure instead of reorganizing the codebase around the new change. Before expanding scope, name the current requirement that forces each additional edit.

Keep the diff cohesive. Omit unrelated cleanup, formatting, renaming, dependencies, compatibility shims, documentation rewrites, and drive-by refactors. When an adjacent flaw blocks the requested behavior, make the smallest enabling correction and keep it visibly tied to the task.

## Produce the artifact

Complete the requested code, patch, or review before explaining it. A diagnosis, plan, or recommendation is not a substitute for an implementation when implementation was requested.

Catch errors only to recover, translate at a boundary, preserve useful context, or guarantee cleanup. Never turn failure into empty success. Respect stated scale; avoid buffering, unbounded concurrency, leaks, and ignored cancellation.

## Remove generated residue

Review the diff as a skeptical maintainer. For every added file, helper, option, branch, validation check, comment, and dependency, name the current requirement it serves. Remove it when there is no concrete answer.

Remove comments that narrate code, template local names when domain vocabulary is available, ceremonial headings, feature tours, unsupported examples, alternative implementations, speculative caveats, and dead or placeholder code. Do not compress readable code into clever code. Keep comments for non-obvious invariants, external quirks, compatibility constraints, and deliberate trade-offs. Match the requested artifact; when asked for code only, return code only.

## Verify and report

Use the repository's existing checks. Test observable behavior and meaningful failure paths; add a regression test for a bug when practical. Do not install a framework for one change, weaken expectations, or replace a failing real test with mock success. Inspect the final diff for accidental scope and incomplete integration.

In review mode, report each independent merge-blocking failure briefly, even when one correction fixes several. Tie it to an observable consequence and the smallest correction. Omit style already enforced by tooling and any claim whose premise is merely “likely,” “probably,” or “might be required.”

In the handoff, state the changed behavior, checks actually run and their outcomes, and limitations affecting confidence. Distinguish inspection from execution. Do not turn missing verification into unsolicited advice or next steps. Omit generic quality claims and repeated summaries.
