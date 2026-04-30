# Agent Skills

This repository contains **agent skills** I’ve created and reuse across my projects.

They’re meant to work with **Claude**, **Cursor**, and other tools that support a “skills” folder pattern.

## Usage

Copy/paste a skill folder from this repo into your project’s skills directory.

- **Claude-style**: copy into your project’s `.claude/skills/`
- **Cursor-style (if you use a skills folder)**: copy into the equivalent skills folder your setup expects

Example:

```bash
cp -R ".claude/skills/<skill-folder>" "/path/to/your-project/.claude/skills/"
```

## Skills in this repo

- **`flutter-structure-guide`**: Enforces a detailed Flutter app folder structure and placement rules (Riverpod + Hive + Dio + secure storage). Source: `.claude/skills/flutter-structure-guide/SKILL.md`
- **`code-decoder`**: Helps explain AI-generated code by building a mental model: system context, block-by-block intent, risks/gotchas, and a concrete test suggestion. Source: `.claude/skills/code-decoder/SKILL.md`
- **`smart-commit`**: Sanity-checks staged changes, suggests a Conventional Commit message, and walks through committing (optionally pushing) in one flow. Source: `.claude/skills/smart-commit/SKILL.md`
- **`architecture-decoder`**: Explains application architecture and end-to-end flow from code and configs, highlighting security, scaling/performance, reliability, and concurrency risks. Source: `.claude/skills/architecture-decoder/SKILL.md`
