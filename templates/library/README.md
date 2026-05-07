# Kirby Library — Copy Abilities

Skills extracted from external sources, rewritten for your agent's context. These are capabilities your agent has access to but doesn't run by default — reach for them when the task calls for it.

Use `/kirby use [name]` to activate one. Use `/kirby powers` to see the full roster.

## Roster

| Ability | Category | Triggers | Absorbed | Source | Uses |
|---------|----------|----------|----------|--------|------|

_No copy abilities yet — feed me something with `/kirby`._

## Skill file format

Each library skill uses this frontmatter:

```yaml
---
name: Ability Name
slug: lowercase-hyphenated
source: [URL or description]
absorbed: YYYY-MM-DD
category: [workflow | technique | pattern | tool-config | mental-model | prompt-pattern]
triggers: [phrases or situations that signal this skill should be activated]
times_used: 0
---
```

The `triggers` field documents when to reach for this ability — phrases you might say, or situations that call for it. It makes the library navigable without needing to remember exact skill names.

## Promotion & retirement

- **Promotion:** Library skills used frequently are candidates for core absorption.
- **Retirement:** Use `/kirby spit [name]` to retire skills that no longer serve you.
