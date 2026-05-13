# Codex Simplify Skill

A Codex skill for reviewing changed code through three lenses:

- code reuse
- code quality
- efficiency

The skill starts from the current diff or a user-specified focus, identifies high-confidence simplification opportunities, and applies only behavior-preserving fixes.

## Repository Layout

```text
.
├── .codex-plugin/plugin.json
└── skills/
    └── simplify/
        └── SKILL.md
```

## Usage

Install this repository as a Codex plugin or copy `skills/simplify` into your Codex skills directory, then invoke:

```text
Use $simplify to review my recent changes.
```

## License

MIT
