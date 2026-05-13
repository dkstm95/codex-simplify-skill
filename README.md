# Codex Simplify Skill

A portable Codex skill for reviewing changed code through reuse, quality, and efficiency lenses, then applying only high-confidence
behavior-preserving simplifications.

## What It Does

`simplify` starts from the current Git diff, PR diff, branch diff, or a user-specified file/focus. It looks for:

- reused code that should use existing local utilities
- unnecessary complexity, duplicate state, wrapper layers, or stringly typed logic
- avoidable inefficiency such as repeated work, N+1 calls, missing cleanup, or no-op updates

The skill is intentionally conservative. It should preserve behavior, error handling, public interfaces, permissions, and transaction
boundaries.

## Installation

Place `SKILL.md` in your Codex skills directory, for example:

```text
~/.codex/skills/simplify/SKILL.md
```

Or keep it inside a repository-specific skills folder if your Codex setup loads project-local skills.

## Usage

Use $simplify to review my recent changes.

Other useful prompts:

Use $simplify to reduce duplication in this diff.
Use $simplify to check this branch for memory efficiency issues.
Use $simplify on src/components/Editor.tsx.

## Source

This skill was adapted from an internal workflow and generalized for public use. It does not depend on any company-specific repositories,
paths, tools, or domain rules.

## License

MIT
