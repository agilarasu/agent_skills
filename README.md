# Agent Skills

This repository contains **agent skills** I’ve created and reuse across my projects.

They’re meant to work with **Claude**, **Cursor**, and other tools that support a “skills” folder pattern.

## Usage

Copy/paste a skill folder from this repo into your project’s skills directory.

- **Claude-style**: copy into your project’s `.claude/skills/`
- **Cursor-style (if you use a skills folder)**: copy into the equivalent skills folder your setup expects

Example:

```bash
cp -R ".claude/skills/flutter-structure-guide" "/path/to/your-project/.claude/skills/"
```

## Skills in this repo

- **`flutter-structure-guide`**: Enforces a detailed Flutter app folder structure and placement rules (Riverpod + Hive + Dio + secure storage). Source: `.claude/skills/flutter-structure-guide/SKILL.md`

