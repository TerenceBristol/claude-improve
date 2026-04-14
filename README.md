# /improve

A self-improving retrospective skill for Claude Code. One command makes your AI agents get better after every conversation.

`/improve` reviews your conversations, detects patterns in your feedback, cross-references against your config files, and suggests targeted improvements — one at a time, with your approval.

The model stays the same. The instructions get smarter.

> **v2.0.0** — Now with scope selection (full scan vs current conversation only), 8 structured config health checks, hook promotion for enforcement gaps, and cross-level analysis. See [Releases](https://github.com/TerenceBristol/claude-improve/releases) for the full changelog.

---

## Quick Start

**1. Clone this repo**

```bash
git clone https://github.com/TerenceBristol/claude-improve.git
```

**2. Copy the skill to your Claude Code commands**

```bash
cp claude-improve/improve.md ~/.claude/commands/improve.md
```

**3. Run it in any Claude Code project**

```
/improve
```

That's it. The skill works on any project with configuration files (CLAUDE.md, skills, agents, memory, frameworks). No dependencies, no setup, no API keys.

---

## How It Works

`/improve` starts by asking what scope you want — **full scan** (history + current conversation) or **current conversation only** — then runs its analysis:

| Phase | What It Does |
|-------|-------------|
| **Scope Selection** | Choose "Historical + current conversation" for the full scan, or "Current conversation only" for a quick session-focused retrospective. |
| **1. Discovery** | A background agent maps every config file at both project and global levels — CLAUDE.md, skills, agents, frameworks, memory, settings files, and `.claude/rules/`. |
| **2. History Scan** | Another background agent reads your last 5 sessions, extracts user messages, and filters for corrections, praise, friction, and explicit feedback. *(Full scope only)* |
| **3. Live Analysis** | Analyzes your current conversation for 9 different signal types — corrections, praise, capability gaps, techniques that worked, and more. |
| **4. Cross-Reference** | Reads your actual config files and checks for enforcement gaps (with hook promotion), recurring patterns, and 8 structured config health checks including size thresholds, memory consolidation, skill consolidation, and cross-level analysis. |
| **5. Present Findings** | Each finding shows up one at a time, ranked by impact. You accept, reject, or modify. Nothing changes without your approval. |
| **6. Apply** | Writes the approved changes — edits to config files, new hooks in settings.json, rule extractions, memory merges, skill creation, and memory entries. |

Phases 1 and 2 run in parallel as background agents. Phase 3 runs simultaneously. This means the skill starts producing findings quickly.

---

## What It Detects

`/improve` looks for 9 types of signals across your conversations:

| Signal | What It Means |
|--------|--------------|
| **Corrections** | You corrected the assistant's behavior or output |
| **Praise** | You confirmed an approach worked well |
| **Friction** | Multiple attempts were needed to get something right |
| **Capability Gaps** | You did something manually that could be automated |
| **Behavioral Patterns** | Tone issues, wrong assumptions, over-explaining |
| **Targeted Feedback** | Arguments you passed directly to `/improve` |
| **Workflow Preferences** | Repeated processes the assistant should learn |
| **Techniques** | Approaches that worked well and should be documented |
| **User Interaction** | Gentle suggestions for how you can work more effectively with your assistant |

---

## Priority System

Findings are ranked into 9 tiers, from highest to lowest priority:

1. **Targeted** — Feedback you explicitly passed as arguments
2. **Critical** — Enforcement gaps (rules that exist but aren't being followed)
3. **Promotion** — Patterns repeated 3+ times that should become documented rules
4. **Improvement** — Direct corrections from the current conversation
5. **Technique** — Successful approaches worth documenting
6. **Maintenance** — Config health issues (bloat, contradictions, staleness)
7. **Reinforcement** — Things working well that should be acknowledged
8. **New Skill** — Capability gaps that could become new skills or agents
9. **User Coaching** — Gentle suggestions for the human side of the collaboration

---

## Works On Any Project

`/improve` is designed to work on any Claude Code project structure. It automatically discovers your config files — whether you use CLAUDE.md, skills, agents, frameworks, memory, or any combination.

It's especially useful for non-coding assistants (content, product, YouTube, research) where the configuration is in plain English and improvements are immediately noticeable.

---

## Community Credits

This skill was built by studying six existing approaches from the Claude Code community. Each one contributed a key idea:

- **[ChristopherA's Bootstrap Seed](https://gist.github.com/ChristopherA/fd2985551e765a86f4fbb24080263a2f)** — Progressive evolution: patterns promote to rules over time
- **[bokan's /self-improvement](https://github.com/bokan/claude-skill-self-improvement)** — Parallel sub-agents for analysis, ranked friction patterns with evidence
- **[AccidentalRebel's Session Retrospective](https://github.com/accidentalrebel/claude-skill-session-retrospective)** — .jsonl reading approach, "techniques discovered" as a signal category ([blog post](https://www.accidentalrebel.com/building-a-session-retrospective-skill-for-claude-code.html))
- **[Sionic AI /retrospective](https://huggingface.co/blog/sionic-ai/claude-code-skills-training)** — "Write while context is fresh" principle
- **[One-Prompt Reflection Pattern](https://dev.to/aviad_rozenhek_cba37e0660/self-improving-ai-one-prompt-that-makes-claude-learn-from-every-mistake-16ek)** — Rule-writing quality standards (NEVER/ALWAYS, lead with WHY, concrete examples)
- **[Human-AI Retrospective](https://dev.to/gjergji_make/running-retrospectives-with-ai-treating-your-model-like-a-teammate-3e09)** — The "human improvement areas" dimension

---

## Watch the Video

<!-- TODO: Update with actual video URL after publishing -->
This skill was introduced in **"I Made All My AI Agents Self-Improving"** on YouTube.

Watch the full breakdown, live demo, and the story behind why this was built:

**[Watch on YouTube](https://youtube.com/@terencebristol)** | **[Subscribe](https://youtube.com/@terencebristol?sub_confirmation=1)**

---

## License

MIT — use it, modify it, make it your own.
