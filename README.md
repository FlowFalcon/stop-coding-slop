# Stop Coding Slop

A skill for keeping AI-assisted coding correct, proportional, and honest.

## What this is

AI-generated code has its own tells: unnecessary abstractions, invented APIs, comments that narrate the obvious line below them, dead code left behind, and confident handoffs that report success no one actually verified. This skill gives Claude (or any LLM that reads the Agent Skills format) a priority-ordered discipline for catching those patterns, so the output stays proportional to what was actually asked and matches how the repository already works.

## Skill structure

```
stop-coding-slop/
├── SKILL.md         # Core instructions
├── README.md
├── LICENSE
└── CHANGELOG.md
```

## Quick start

**Claude Code:** drop the folder into `~/.claude/skills/` (personal) or `.claude/skills/` (project). Claude picks it up automatically.

**claude.ai:** zip the folder and upload it under Settings → Features. Requires a Pro, Max, Team, or Enterprise plan with code execution enabled.

**Claude API:** upload through the `/v1/skills` endpoint (requires the code execution tool beta), or paste `SKILL.md` directly into your system prompt for a lighter setup.

**Other agents:** `SKILL.md` is plain Markdown with YAML frontmatter, the open Agent Skills format, so it also works as a system prompt or context file for other tools that read the same convention.

## What it enforces

**Priority order** — correctness first, then security and data integrity, then repository conventions, then proportional design, and only then naming and comment polish. Earlier priorities are never traded away for later ones.

**Evidence over invention** — inspect the actual implementation, callers, tests, and conventions before changing anything. No invented APIs, no assumed package behavior, no fabricated rationale.

**Proportional scope** — implement the current requirement completely, without speculative layers, unused flags, or premature abstraction, and without going so minimal that validation, tests, or failure handling go missing.

**Reuse by semantics** — match existing validation, error, and lifecycle behavior before reusing code, instead of forcing reuse just because two things share a name.

**Honest reporting** — report only the checks that actually ran and their real outcomes, distinguishing inspected, inferred, and unverified facts.

See [SKILL.md](SKILL.md) for the full rule set, including the boundary-preservation rules for input handling, error handling, and scale.

## Author

_Add your name or GitHub handle here._

## License

MIT. See [LICENSE](LICENSE).
