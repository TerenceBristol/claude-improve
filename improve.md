---
name: improve
description: Review the current conversation and recent session history to suggest improvements to the project's configuration — CLAUDE.md, skills, frameworks, memory, agents. Always does a full sweep. Arguments add extra weight to specific areas. Works on any repo structure.
---

# Retrospective

Review conversation + recent history, cross-reference against config files, present improvement suggestions one at a time.

**Announce:** "Starting retrospective... Launching discovery and history scan agents."

**Arguments = targeted feedback.** Always full sweep. Args get highest priority but don't limit scope. Works mid-conversation or end-of-session.

## Phase 1 & 2: Discovery + History Scan (Background, Parallel)

Launch BOTH agents in background simultaneously, then immediately proceed to Phase 3.

### Discovery Agent (Explore, background)

Prompt the agent to search for and catalog ALL config-like files in this project:
- CLAUDE.md (root or .claude/)
- .claude/skills/ or capabilities/ directories
- .claude/agents/ directory
- Shared frameworks, guardrails, style guides (shared/, frameworks/, etc.)
- Memory files at ~/.claude/projects/[project-path]/memory/
- Voice/brand files (vault/, brand/, etc.)
- Any other instruction-like .md files governing behavior

Return a "config map": list of files with purpose, organized by type.

### History Scan Agent (general-purpose, background)

Prompt the agent to:

1. Write a bash script that:
   - Lists .jsonl session files from `~/.claude/projects/[project-path]/`
   - Sorts by modification date, takes 5 most recent (excluding current session)
   - For each file, extracts ONLY lines containing user messages (type "human") — skip assistant responses and tool calls
   - From user messages, filters for feedback signals:
     - Corrections: "no", "don't", "stop", "not that", "wrong", "actually", "instead"
     - Praise: "yes", "perfect", "exactly", "great", "love", "nice"
     - Explicit feedback: "improve", "better", "should", "could you", "I wish", "next time"
     - Frustration: repeated requests, "again", "I already said"
   - Saves extracted messages with session date to a temp file
   - No cap on signals — let all relevant feedback through

2. Read temp file and organize findings:
   - Tag each with: session date, brief context quote
   - Group by type: corrections, praise, friction, capability gaps
   - Note recurring patterns across sessions (same feedback 2+ times = promotion candidate)

Return categorized findings with source citations. Concise summaries, not raw data.

## Phase 3: Current Conversation Analysis (Foreground)

**Announce:** "Analyzing current conversation for patterns, feedback, and techniques..."

Analyze the conversation already in context for:

| Signal | What to Look For |
|--------|-----------------|
| **Corrections** | User corrected behavior, said "no", "don't", "stop", asked to redo |
| **Praise** | User confirmed approach, said "yes", "perfect", accepted without pushback |
| **Friction** | Multiple attempts, confusion, back-and-forth to get it right |
| **Capability gaps** | User did things manually, asked for something assistant couldn't do |
| **Behavioral patterns** | Tone issues, over/under-explaining, wrong assumptions |
| **Targeted feedback** | Arguments passed to /improve — HIGHEST PRIORITY |
| **Repeated workflows** | Multi-step manual processes that could become a skill |
| **Techniques discovered** | Novel approaches that worked well — new methods, clever tool usage |
| **User interaction patterns** | User prompting styles that led to better/worse results |

**Low-signal:** If minimal feedback in current conversation, say "No significant findings from this session" and proceed to history/config findings.

## Phase 4: Cross-Reference & Categorize

**Announce:** "Cross-referencing findings against config files..."

Wait for background agents to complete. Then read each config file from the config map.

### 4a: Enforcement Gap Detection

For each existing rule in config files, check if the current conversation shows it being violated. Enforcement gaps mean the rule needs strengthening (emphasis, position, examples) — not removal.

### 4b: Progressive Evolution (Pattern Promotion)

When history scan shows the same feedback across 2+ sessions, suggest PROMOTING:
- Memory file → CLAUDE.md rule
- Buried rule → top of CLAUDE.md with NEVER/ALWAYS emphasis
- Implicit pattern → explicit documented rule with examples
- Soft guideline → hard rule with enforcement language

### 4c: Config Health Check

- **Bloat:** CLAUDE.md excessively long? Redundant rules across files?
- **Contradictions:** Two skills/rules giving conflicting instructions?
- **Staleness:** Memory files referencing things that no longer exist?
- **Structure:** Files grown organically without cleanup?

### 4d: Categorize All Findings

| Category | When to Use | Priority |
|----------|-------------|----------|
| **Targeted** | From user's explicit /improve args | 1st |
| **Critical** | Caused errors, repeated correction, enforcement gaps | 2nd |
| **Promotion** | Recurring cross-session pattern needing stronger rule | 3rd |
| **Improvement** | Enhancement to existing rules/skills/behaviors | 4th |
| **Technique** | Novel approach that worked — document for reuse | 5th |
| **Maintenance** | Config health: bloat, contradictions, staleness | 6th |
| **Reinforcement** | Worked well — strengthen existing documentation | 7th |
| **New Skill** | Repeated pattern that could become a dedicated skill | 8th |
| **User Coaching** | Gentle suggestion for better user-AI interaction | Last |

Skip findings that are already documented AND being followed.

## Phase 5: Present Findings

**Announce:** "Found N findings across M categories. Presenting one at a time, most impactful first."

### Rule-Writing Quality Standards

All proposed rule changes MUST:
- Start critical rules with **NEVER** or **ALWAYS**
- Lead with **WHY** so edge cases can be judged
- Use precise language: "try to" → "always", "consider" → "must"
- Include a concrete example when not self-evident
- Keep concise — one clear sentence beats a paragraph

### Presentation

Present each finding via **AskUserQuestion**:

**Question format:** "[Tier] — [Source: current conversation / past session date] — [Description of finding and proposed change]. File: [full path]. Proposed: [what to add/modify/remove]"

**Options:** Accept / Reject / Modify

**Order:** Targeted → Critical → Promotion → Improvement → Technique → Maintenance → Reinforcement → New Skill → User Coaching

If 8+ findings, after presenting 5, ask: "Continue with remaining findings, or apply what we have so far?"

## Phase 6: Apply Changes

**Announce:** "Applying N approved changes across M files..."

1. Group approved changes by file
2. Edit existing files with approved modifications
3. Create new files if needed (new memory entries, new skill stubs)
4. For feedback-type findings, ALSO save as memory files:
   - File: `feedback_[topic].md` in project's memory directory
   - Frontmatter: name, description, type: feedback
   - Content: rule + **Why:** + **How to apply:**
5. Update MEMORY.md index if new memory files created
6. Present a summary table in the conversation:

   ## Changes Applied

   | # | File | Change | Category |
   |---|------|--------|----------|
   | 1 | path/to/file.md | Brief description of what changed | Category |

   N changes across M files.

   - **File**: short relative path (not full absolute)
   - **Change**: concise action (e.g. "Added rule: …", "Strengthened: X → Y", "New memory: …")
   - **Category**: tier from Phase 4d (Critical, Promotion, Improvement, etc.)
7. Ask if user wants to commit changes
