# Kirby

An absorption engine for AI agents. Kirby evaluates external content — articles, workflows, configs, screenshots, repos — and selectively integrates what's useful into your agent's configuration. Named after the video game character who gains abilities from what it consumes.

The core idea: you should be able to throw anything at your agent and have it evaluate the source, decide what (if anything) would make the agent better, and formalize the improvement — all while preserving the agent's existing identity and preferences.

## What it does

Kirby runs a structured evaluation pipeline on any source you give it:

1. **Source Analysis** — Evaluate standalone. Credibility, quality, discrete ideas extracted.
2. **Compatibility Analysis** — Compare each idea against your agent's current configuration. What aligns, contradicts, is novel, or supersedes something existing?
3. **Absorption Proposal** — Three outcomes for each idea: absorb into core config, add to the skill library, or reject. You approve before anything changes.
4. **Implementation** — Apply approved changes, log everything, update meta-learning.

## Modes

| Command                  | What it does                                                  |
| ------------------------ | ------------------------------------------------------------- |
| `/kirby [source]`        | Evaluate a single source and propose absorptions              |
| `/kirby feast [sources]` | Batch-evaluate multiple sources with cross-source synthesis   |
| `/kirby powers`          | Show the full ability roster                                  |
| `/kirby use [ability]`   | Activate a library skill for the current task                 |
| `/kirby spit [ability]`  | Reverse an absorption or retire a library skill               |

### Copy Abilities

Every library skill gets a copy ability name — short, evocative, memorable. Like Kirby's in-game abilities (Fire, Sword, Beam), the name instantly suggests what the skill does. `/kirby powers` shows your roster. `/kirby use [name]` activates one.

### Feast

Process multiple sources together. The value is cross-source synthesis — consensus, contradictions, and patterns that only emerge from the intersection. One article is an opinion. Three articles is a trend you can evaluate structurally.

### Spit

The inverse of absorb. If something isn't working or you've outgrown it, spit it out. Spits are logged as identity signals — "this fit once but stopped working" is richer than "this never fit." Making absorption reversible lowers the stakes on saying yes.

## Three outcomes

| Outcome      | What it means                              | Where it goes                    |
| ------------ | ------------------------------------------ | -------------------------------- |
| **Absorb**   | Integrate into your agent's core config    | Target file in your agent setup  |
| **Library**  | On-demand copy ability (not always active) | `kirby/library/[name].md`        |
| **Reject**   | Not useful (logged as identity signal)     | `kirby/rejections.md`            |

When in doubt between Absorb and Library, Kirby defaults to Library. Things can be promoted up. Premature core absorption is harder to untangle.

## How to install

### 1. Copy the skill

Copy `SKILL.md` to your Claude Code skills directory:

```bash
mkdir -p ~/.claude/skills/kirby
cp SKILL.md ~/.claude/skills/kirby/SKILL.md
```

### 2. Configure the skill

Open `~/.claude/skills/kirby/SKILL.md` and fill in the **Configuration** section at the top of the instructions. You need to tell Kirby:

- **Where your agent configuration lives** — the root directory of your agent's config files
- **What your identity layers are** — which files define who your agent IS (high protection) vs. how it works (open to evolution) vs. what it can do (additive)
- **What to read for different content types** — a mapping from source topics to relevant config files

The skill ships with a placeholder configuration. Replace it with your paths and files.

### 3. Set up the tracking directory

Copy the templates into your agent's configuration directory (wherever that lives for you):

```bash
# Adjust the destination to wherever your agent config lives
AGENT_DIR="~/your-agent"

mkdir -p "$AGENT_DIR/kirby/library"
cp templates/absorptions.md "$AGENT_DIR/kirby/"
cp templates/rejections.md "$AGENT_DIR/kirby/"
cp templates/spits.md "$AGENT_DIR/kirby/"
cp templates/meta.md "$AGENT_DIR/kirby/"
cp templates/TODO.md "$AGENT_DIR/kirby/"
cp templates/library/README.md "$AGENT_DIR/kirby/library/"
```

### 4. Use it

```
/kirby https://example.com/interesting-article
/kirby feast [url1] [url2] [url3]
/kirby powers
/kirby use Vault
/kirby spit Vault
```

## How the meta-learning works

Kirby tracks patterns across runs in `meta.md`. Over time, it learns what you tend to accept and reject, which source types produce useful absorptions, and what your identity boundaries are. This calibrates future proposals — Kirby gets better at knowing what to suggest as you use it.

## How identity protection works

Kirby treats your agent's configuration as having three layers with different protection levels:

- **Core (high protection)** — Who your agent IS. Identity, values, communication style, non-negotiable rules. Kirby will never overwrite these. It can propose refinements, but flags them prominently and requires explicit approval.
- **Operational (open to evolution)** — How your agent WORKS. Workflows, standards, processes. Kirby proposes changes freely; you approve or reject.
- **Additive (lowest friction)** — What your agent CAN DO. Library skill candidates. Techniques, patterns, and tools that expand capability without changing defaults.

You define which of your files belong to which layer in the configuration section.

## Project structure

```text
kirby/
  README.md                  <- You are here
  SKILL.md                   <- The skill file (copy to ~/.claude/skills/kirby/)
  templates/                 <- Tracking files (copy to your agent's config)
    absorptions.md           <- Log of accepted absorptions
    rejections.md            <- Log of evaluated-but-rejected content
    spits.md                 <- Log of reversed/retired abilities
    meta.md                  <- Self-learning patterns across runs
    TODO.md                  <- Open items
    library/
      README.md              <- Copy ability roster
```

## Requirements

- [Claude Code](https://claude.ai/claude-code) (the skill is a Claude Code skill file)
- An agent configuration of some kind — a CLAUDE.md, a skills directory, markdown files that define how your agent operates. Kirby needs something to evaluate against and absorb into.

## Credits

Built by [Mara DeWald](https://github.com/maradewald) as part of [Agent Mara](https://github.com/maradewald), a design leadership agent operating system. The concept: real people improve by learning from others. Agents should too.
