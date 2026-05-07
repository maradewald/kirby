---
name: kirby
description: Absorption engine for your AI agent. Evaluates external sources (articles, workflows, configs, screenshots, repos) and selectively integrates useful patterns into your agent's configuration or skill library. Modes — `/kirby [source]` to evaluate and absorb, `/kirby feast` for batch processing multiple sources, `/kirby powers` to see the ability roster, `/kirby use [ability]` to activate a library skill, `/kirby spit [ability]` to reverse an absorption. Also trigger on "kirby this" or "absorb this."
---

# Kirby — Absorption Engine

Kirby evaluates external content and selectively absorbs what improves your agent — extracting abilities while preserving identity. Named after the character who gains abilities from what it consumes.

Three possible outcomes for any piece of content:
- **Absorb** — Integrate into your agent's core configuration (permanent, changes default behavior)
- **Library** — Create a Mara-fied skill in the Kirby library (available on demand, doesn't change defaults)
- **Reject** — Not useful (log why — rejections are identity signals)

---

## Configuration

**Fill this in before first use.** Tell Kirby where your agent lives and what matters.

### Agent root directory
```
AGENT_DIR: [/path/to/your/agent/config]
```

### Kirby tracking directory
```
KIRBY_DIR: [AGENT_DIR]/kirby
```

### Identity layers

Define which files belong to which protection level:

**Core (high protection)** — Who your agent IS. Never overwrite. Only refine with strong justification.
```
CORE_FILES:
  - [path to identity/values files]
  - [path to communication style preferences]
  - [path to non-negotiable rules/guardrails]
```

**Operational (open to evolution)** — How your agent WORKS. Propose changes freely, implement with approval.
```
OPERATIONAL_FILES:
  - [path to workflows]
  - [path to standards]
  - [path to process definitions]
  - [path to CLAUDE.md or equivalent]
```

**Additive (lowest friction)** — Library skill candidates. Techniques and tools that expand capability.
```
ADDITIVE: kirby/library/
```

### Content-to-config mapping

When Kirby reads a source, it needs to know which of your config files are relevant. Map source topics to files:

```
CONTENT_MAP:
  workflow/process/productivity:
    - [path to workflow files]
    - [path to process definition]
  design/philosophy/values:
    - [path to identity files]
  quality/standards:
    - [path to standards files]
  engineering/code:
    - [path to engineering standards]
  communication/voice:
    - [path to communication preferences]
  agent/AI/prompts:
    - [path to agentic operations or prompt files]
  tools/integrations:
    - [path to tool configs, settings files]
```

---

## Modes

| Command | What it does |
|---------|-------------|
| `/kirby [source]` | Evaluate a single source and propose absorptions |
| `/kirby feast [sources]` | Evaluate multiple sources together with cross-source synthesis |
| `/kirby powers` | Show the full ability roster — core absorptions, library skills, and retired abilities |
| `/kirby use [ability]` | Activate a library skill for the current task |
| `/kirby spit [ability]` | Reverse a core absorption or retire a library skill |

---

## `/kirby [source]` — Standard Absorption

The core pipeline. Evaluate one source, propose what to absorb.

### Setup

**1. Parse and fetch the source**

- **URL** — Fetch with WebFetch
- **File path** — Read the file
- **Inline text** — Use directly from the conversation
- **Screenshot/image** — Read the image file and describe what you see

**2. Read your agent's current state**

Always read:
- The main agent configuration file (CLAUDE.md or equivalent)
- Any guardrails or non-negotiable rules
- `[KIRBY_DIR]/meta.md` — Self-learning from past runs
- `[KIRBY_DIR]/library/README.md` — Current ability roster

Then, based on the source content, use the CONTENT_MAP to read relevant config files.

### Phase 1: Source Analysis

Evaluate the source on its own merits, independent of your agent.

- **Classification:** What type of content? (workflow, technique, tool config, file structure, philosophy, prompt pattern, mental model, etc.)
- **Credibility:** Who created this? Backed by experience, evidence, or vibes?
- **Core ideas:** Extract discrete, actionable patterns. Don't summarize — decompose. One article might contain 5 distinct ideas.
- **Strengths:** What's well-reasoned, novel, or clearly valuable?
- **Weaknesses:** What's handwavy, context-dependent, overcomplicated, or unproven?
- **Quality signal:** High / Medium / Low

If quality is **Low**, report the assessment and ask whether to proceed. Don't burn time on Phases 2-3 for noise.

### Phase 2: Compatibility Analysis

For each discrete idea from Phase 1:

| Verdict | Meaning |
|---------|---------|
| **Aligns** | Your agent already does this. Note where. |
| **Contradicts** | Conflicts with existing config. Name the specific conflict. |
| **Novel** | Genuinely new. Not covered anywhere. |
| **Supersedes** | Better version of something your agent already does. Name what it replaces. |

**Identity boundary check:** Use the CORE_FILES, OPERATIONAL_FILES, and ADDITIVE definitions from Configuration. If a source idea targets a Core file, flag it explicitly and explain the risk.

### Phase 3: Absorption Proposal

Present a scannable summary:

```
## Kirby Report: [source title/description]

**Source quality:** [High/Medium/Low] — [one line]
**Relevance:** [High/Medium/Low] — [one line]

### Proposed absorptions

**Absorb (integrate into core)**
1. **[What]** -> `[target file]`
   [Why this improves your agent]
   Confidence: [High/Medium/Low]

**Library (copy ability)**
2. **[Ability Name]** — [What] -> `kirby/library/[slug].md`
   [What this gives your agent on demand]

**Reject**
3. **[What]** — [Why not]

---
Approve all? Or tell me which to modify, skip, or recategorize.
```

**Categorization:**
- Changes default behavior -> **Absorb**
- Useful situationally, not always-on -> **Library**
- Doesn't improve the agent or conflicts with identity -> **Reject**

**When in doubt between Absorb and Library, default to Library.** Things promote up. Premature core absorption is harder to untangle.

**Calibrate with meta-learning:** Check `meta.md` before finalizing. Match proposals against past acceptance/rejection patterns. Don't repeatedly propose things that have been established as unwanted.

### Phase 4: Implementation

After approval:

**Core absorptions:**
1. Read the target file
2. Make the change
3. Create a decision record if the change is substantial (if your agent uses decision records)

**Library additions:**
1. Create a file in `kirby/library/` (see Library Skill Format below)
2. Update `kirby/library/README.md` roster

**After all changes:** Run logging, version bump (if applicable), meta-learning update, and report saving (see Shared Operations).

---

## `/kirby feast [sources]` — Batch Absorption

Process multiple sources as a cohort. The value is in cross-source synthesis — consensus, contradictions, and signal that only emerges from the intersection.

### How to invoke

Provide multiple sources in one message: multiple URLs, a mix of URLs and file paths, or "here are three articles about [topic]."

### Process

**Step 1: Individual Source Analysis**
Run Phase 1 for each source independently.

**Step 2: Cross-Source Synthesis**

Before Phase 2, synthesize across sources:

- **Consensus:** What do multiple sources agree on? Higher confidence.
- **Contradictions:** Where do they disagree? Name it explicitly. Contradictions are the most interesting signal.
- **Unique contributions:** What does only one source bring? Genuine insight or outlier?
- **Emergent patterns:** What only becomes visible across sources?
- **Quality weighting:** Higher-quality sources get more weight on contradictions.

**Step 3: Unified Proposal**
Run Phases 2-3 on the synthesized ideas. The proposal references which sources support each candidate.

```
## Kirby Feast Report: [topic/description]

**Sources evaluated:** [count]
1. [Source title/URL] — Quality: [H/M/L]
2. [Source title/URL] — Quality: [H/M/L]
3. [Source title/URL] — Quality: [H/M/L]

### Cross-source synthesis
- **Consensus:** [What multiple sources agree on]
- **Contradictions:** [Where they disagree and what that means]
- **Unique finds:** [Single-source ideas worth considering]

### Proposed absorptions

[Same format as standard /kirby, with source attribution per candidate]

---
Approve all? Or tell me which to modify, skip, or recategorize.
```

**Step 4: Implementation**
Same as standard `/kirby`. Note all sources in absorption log. In the report, note this was a feast and list all sources evaluated.

---

## `/kirby powers` — Ability Roster

Show the full Kirby inventory. A roster, not a file listing.

### Process

1. Read `[KIRBY_DIR]/absorptions.md`
2. Read `[KIRBY_DIR]/library/README.md`
3. Read `[KIRBY_DIR]/spits.md`
4. Read `[KIRBY_DIR]/meta.md`

### Output format

```
## Copy Abilities

### Core (always active)
Permanently absorbed into your agent's configuration.

| Absorbed | What | Target | Source |
|----------|------|--------|--------|
| YYYY-MM-DD | [Description] | `[file]` | [source] |

### Library (on demand)
Available when you need them. Use `/kirby use [name]` to activate.

| Ability | Category | Absorbed | Source | Uses |
|---------|----------|----------|--------|------|
| [Name] | [category] | YYYY-MM-DD | [source] | [count or --] |

### Retired
Abilities that were spit out.

| Ability | Was | Retired | Why |
|---------|-----|---------|-----|
| [Name] | [core/library] | YYYY-MM-DD | [reason] |

---
**Runs completed:** [N] | **Last run:** [date]
```

If any section is empty, show it with a note like *"No library abilities yet — feed me something with `/kirby`."*

---

## `/kirby use [ability]` — Activate a Library Skill

Pull a library skill into the current conversation.

### Process

1. Fuzzy-match the ability name against `kirby/library/`
2. Read the file
3. Present the "How to apply" and "When to reach for this" sections
4. Note the activation in `meta.md` — frequent use is a promotion signal

### If not found

- Could be a core absorption (already active)
- Could be retired (offer to re-absorb)
- Could be a misspelling (fuzzy match)
- If genuinely missing: "That ability doesn't exist yet. Want to `/kirby` a source to create it?"

---

## `/kirby spit [ability]` — Reverse / Retire

The inverse of absorb. Spits are the strongest identity signal — they capture what worked once but stopped fitting.

### Process

**1. Identify the target**
Parse what's being spit. Could be a library skill by name or a core absorption by description.

**2. Find the record**
Check library README and absorptions log. Confirm the match.

**3. Understand why**
Ask briefly: "What stopped working?" The answer is the identity signal.

**4. Propose the reversal**

For **library skills:** Remove the file and the roster entry.

For **core absorptions:** Read the target file, identify the absorbed content, propose the specific revert as a diff. The file may have changed since absorption — don't blindly revert, show what you'd change and get approval.

**5. Post-spit operations**
- Log in `spits.md`
- Update `meta.md` with the spit pattern and identity signal
- Bump version if applicable

---

## Shared Operations

### Library Skill Format

Every library skill gets a **copy ability name** — short, evocative, memorable. Think Kirby game abilities: functional but with character. Examples: "Vault" for knowledge storage, "Scout" for research, "Architect" for structural patterns, "Mirror" for review techniques.

```markdown
---
name: [Copy Ability Name]
slug: [lowercase-hyphenated]
source: [URL or description of original source]
absorbed: [YYYY-MM-DD]
category: [workflow | technique | pattern | tool-config | mental-model | prompt-pattern]
triggers: [phrases or situations that signal this skill should be activated]
times_used: 0
---

# [Copy Ability Name]

[What this is and when to use it — 2-3 sentences max]

## How to apply

[The technique/workflow/pattern, adapted for YOUR context and tools. Actionable, not theoretical.]

## When to reach for this

[Specific triggers or situations where this is relevant]

## Limitations

[What this doesn't cover or where it might not apply]
```

Library skills must be **rewritten for your agent's context** — not bookmarked. The source material is the ingredient. The library skill is the dish.

### Logging

After every run that changes state:

**Absorptions** — Append to `kirby/absorptions.md`:
```
- **[YYYY-MM-DD]** — [What] -> `[target]` — Source: [URL or description]
```

**Rejections** — Append to `kirby/rejections.md`:
```
- **[YYYY-MM-DD]** — [What] — [Why] — Source: [URL or description]
```

**Spits** — Append to `kirby/spits.md`:
```
- **[YYYY-MM-DD]** — [Name] ([core/library]) — [Why] — Originally from: [source]
```

### Report Saving

After every source evaluation run (`/kirby` or `/kirby feast`), save a full report and update the run log. Do this after implementation, as part of the same operation as logging.

**Report file** — Create `kirby/reports/YYYY-MM-DD-[slug].md` where slug is a short kebab-case description of the source(s):

```markdown
# Kirby Report — YYYY-MM-DD
## [Source title / description]

**Run type:** Standard | Feast ([N] sources)
**Sources:** [URL(s) or description(s)]
**Source quality:** [High/Medium/Low] — [one line]
**Relevance:** [High/Medium/Low] — [one line]

---

## Phase 1 — Core ideas extracted
[Numbered list of discrete ideas]

## Phase 2 — Compatibility
[Table: Idea | Verdict | Notes]

## [For feast runs only] Cross-Source Synthesis
[Consensus, contradictions, unique contributions]

## Phase 3 — Proposal (outcome: approved / modified / rejected)
[Absorb table, Reject table]

## Identity signals captured this run
[Any new identity signals, or "None"]

## Changes made
[Bullet list of files created/modified]
```

**Run log** — Append a row to `kirby/run-log.md`:
```
| YYYY-MM-DD | [Standard/Feast/Library activation] | [Source URL or ability name] | [Outcome summary. Report: `reports/[filename].md` or N/A] |
```

Library activations (`/kirby use`) and spits (`/kirby spit`) get a run-log row only — no full report file.

### Meta-Learning Update

After every Kirby run, update `kirby/meta.md`:

1. Increment run count and update date
2. Note acceptance/rejection patterns as they emerge
3. Note identity boundary signals from rejections and spits
4. Note calibration signals when proposals are modified (recategorized, edited)
5. Track `/kirby use` activations for promotion signals

---

## Edge Cases

- **Low quality source:** Complete Phase 1, report assessment, ask whether to proceed.
- **Entirely redundant:** Report existing coverage. Note minor refinements if applicable.
- **Contradicts core identity:** Report clearly. Don't propose overwriting core. Suggest as discussion if interesting.
- **Source is someone's CLAUDE.md or agent config:** High-signal. Treat each section as a discrete idea.
- **Source is a screenshot:** Describe, then evaluate visible content.
- **Source is a repo or folder structure:** Evaluate the organizational pattern, not every file.
- **Spit target modified since absorption:** Show current state, propose change, don't auto-revert.
- **Feast sources contradict each other:** Present contradiction prominently. Let the user decide which position aligns.
- **Unused library skills:** Flag in roster — candidates for retirement after significant time.
