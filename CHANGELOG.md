# Changelog

## 1.2.2 — 2026-08-14

- Expanded the English README with a detailed definition of AI coding slop, small diagnostic signals, causes, operating principles, workflow, examples, installation, usage, and evaluation guidance.
- Added `README_id.md` as a complete Indonesian documentation counterpart.
- Added language navigation and corrected repository installation URLs to `FlowFalcon/stop-coding-slop`.
- Restored the public author identity as FlowFalcon (Fathur).

## 1.2.1 — 2026-08-14

- Added an explicit think-before-coding gate so the first plausible implementation is not treated as the answer.
- Required observable success criteria and verification evidence before implementation begins.
- Clarified that simplicity means fewer concepts and less indirection, not blindly minimizing line count.
- Added a surgical-change rule that requires every expanded edit to be tied to the current contract.
- Updated the UI metadata and default prompt to reflect the revised workflow.
- Raised the long-form judge budget and made prompt-declared global types explicit to the judge after a truncated response and false missing-import failures were observed.

## 1.2.0 — 2026-08-13

- Replaced the compact cases with three long-form TypeScript tasks covering an atomic endpoint, a bounded streaming importer, and a transactional cancellation flow.
- Expanded each case to require implementation and regression tests across multiple files so differences in patch scope, comments, boundary handling, and verification are visible.
- Added explicit repository integration contracts to keep the benchmark focused on implementation quality instead of guessed framework APIs.
- Set the target output budget to 4,000 tokens and the judge budget to 1,800 tokens for reliable local evaluation.

## 1.1.0 — 2026-08-13

- Replaced the principle-heavy prompt with a shorter evidence-to-diff workflow.
- Added an explicit generated-residue pass for helpers, branches, validation, comments, naming, prose, and placeholders.
- Tightened reuse decisions around transformation, error, side-effect, transaction, and lifecycle semantics.
- Added guards against guessed imports, framework types, incomplete artifacts, speculative review findings, and unsolicited handoff advice.
- Rebuilt the eval suite around six repository-style cases that do not state the intended anti-slop decision.
- Added `agents/openai.yaml` metadata and validated the skill with the skill-creator tooling.

## 1.0.0 — 2026-08-12

Initial public release.

- Priority order for resolving conflicts between correctness, security, repository conventions, proportional design, and style.
- Evidence-first workflow: inspect before changing, distinguish requirements from assumptions.
- Proportional scope guidance to avoid speculative abstractions and false minimalism.
- Reuse guidance based on matching semantics, not just matching names.
- Boundary-preservation rules for input handling, error handling, and scale.
- Naming and comment guidance focused on meaning over template patterns.
- Honest test-and-report discipline: report only what actually ran.
