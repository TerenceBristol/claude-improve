# /improve

A self-improving retrospective skill for Claude Code. One command makes your AI agents get better after every conversation.

`/improve` reviews your conversations, detects patterns in your feedback, cross-references against your config files, and suggests targeted improvements — presented as a ranked findings list, applied only with your approval.

The model stays the same. The instructions get smarter.

> **v4.0.0** — Two-level learnings (global preferences + per-project state), compact findings-list presentation by default, simplified scope question, and a settled-pattern promotion rule so learnings graduate into your config. See [Releases](https://github.com/TerenceBristol/claude-improve/releases) for the full changelog.

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

## Updating

Pull the latest and re-copy:

```bash
cd ~/path/to/claude-improve && git pull
cp improve.md ~/.claude/commands/improve.md
```

---

## Files Created

`/improve` maintains two learnings files automatically:

- **`~/.claude/improve-learnings.md`** (global) — Cross-project patterns about how you like the skill and rule-writing to work: presentation preferences, scope habits, modify signals. Read at the start of every run in every project.
- **`~/.claude/projects/<project-path>/improve-learnings.md`** (per project) — That project's distilled patterns, deferral counters, and a date-keyed run log. Lives in Claude Code's own per-project data directory under your home folder, so it never touches your repo. Note: `<project-path>` is the project's absolute path with every `/`, space, and `.` replaced by `-` (e.g. `/Users/jane/Code/my-app` → `-Users-jane-Code-my-app`) — the same directory naming Claude Code uses for session files.

Both are auto-managed — no manual editing needed. Delete either anytime to reset. Patterns confirmed across ~5 runs get proposed for promotion into your actual config (skill/CLAUDE.md/settings) and removed from learnings, so the files stay small and your config — not a side file — stays the source of truth. If you used a pre-4.0 version, your existing `~/.claude/improve-learnings.md` keeps working as the global file; project state migrates naturally as runs happen.

---

## How It Works

`/improve` starts by asking what scope you want — **current conversation only** (the default) or **historical + current** (full scan). A third mode, **config audit only** (standalone config maintenance, no conversation analysis), is invoked explicitly with `/improve config audit`. Then it runs its analysis:

| Phase | What It Does |
|-------|-------------|
| **Scope Selection** | "Current conversation only" for a quick session-focused retrospective (default), or "Historical + current conversation" for the full scan. |
| **1. Discovery** | A background agent maps every config file at both project and global levels — CLAUDE.md, skills, agents, frameworks, memory, settings files, and `.claude/rules/`. |
| **2. History Scan** | Another background agent reads your last 5 sessions, extracts user messages, and filters for corrections, praise, friction, and explicit feedback. *(Full scope only)* |
| **3. Live Analysis** | Analyzes your current conversation for 9 different signal types — corrections, praise, capability gaps, techniques that worked, and more. |
| **4. Cross-Reference** | Reads your actual config files and checks for enforcement gaps (with hook promotion), recurring patterns, and 8 structured config health checks including size thresholds, memory consolidation (with promoted-but-not-cleaned and stale memory detection), skill consolidation, and cross-level analysis. |
| **5. Present Findings** | All findings arrive as one compact list, ranked by impact, each with evidence and a recommendation. Genuine decisions get resolved via follow-up questions; ask to "walk me through them" for a per-finding accept/reject/modify flow. Nothing changes without your approval. |
| **6. Apply** | Writes the approved changes — edits to config files, new hooks in settings.json, rule extractions, memory merges, skill creation, and memory entries. |

In full scope, up to three background agents run in parallel (discovery, history scan, and a prior-improve audit that verifies whether previously accepted changes actually landed — its results open Phase 5 as an audit table). Phase 3 runs simultaneously. This means the skill starts producing findings quickly.

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

Findings are ranked into 10 tiers, from highest to lowest priority:

1. **Targeted** — Feedback you explicitly passed as arguments
2. **Critical** — Enforcement gaps (rules that exist but aren't being followed)
3. **Promotion** — Patterns repeated 3+ times that should become documented rules
4. **Content Misplacement** — Instructions or knowledge living in the wrong file
5. **Improvement** — Direct corrections from the current conversation
6. **Technique** — Successful approaches worth documenting
7. **Maintenance** — Config health issues (bloat, contradictions, staleness)
8. **Reinforcement** — Things working well that should be acknowledged
9. **New Skill** — Capability gaps that could become new skills or agents
10. **User Coaching** — Gentle suggestions for the human side of the collaboration

---

## Example Finding

Here's what a finding looks like inside Phase 5's compact list:

```
[Critical | High] — Source: 2026-04-28 — Enforcement gap detected.

Rule "ALWAYS use AskUserQuestion for decisions" is documented in CLAUDE.md
but was violated 3x in recent sessions.

File: ~/.claude/CLAUDE.md
Proposed: Convert to hook (deterministic enforcement).
Recommendation: Convert to hook — this rule keeps slipping as advisory text.
```

Each finding shows its priority tier, confidence level, source, a specific proposed change, and a recommendation. Items needing a genuine decision get a follow-up question; in the opt-in per-finding walkthrough, each finding instead ends with Accept / Reject / Modify options. Nothing happens without your approval.

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

[![Watch on YouTube](https://img.shields.io/badge/YouTube-Watch_the_Video-red?style=for-the-badge&logo=youtube)](https://youtu.be/heLMN5oDHEU?si=LYjQkisDgn4ReWl9)

---

## License

MIT — use it, modify it, make it your own.
