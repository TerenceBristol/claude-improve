---
name: improve
description: Review the current conversation and recent session history to suggest improvements to the project's configuration — CLAUDE.md, skills, frameworks, memory, agents. Always does a full sweep. Arguments add extra weight to specific areas. Works on any repo structure.
---

# Retrospective

Review conversation + recent history, cross-reference against config files, present improvement suggestions one at a time.

**Announce:** "Starting retrospective..."

**Arguments = targeted feedback.** Always full sweep. Args get highest priority but don't limit scope. Works mid-conversation or end-of-session.

## Scope Selection

Before launching any agents, ask the user:

**Question:** "What scope should this retrospective cover?"

**Options (AskUserQuestion):**
- **Historical + current conversation** — Full scan: history from recent sessions, prior /improve audit, plus current conversation analysis
- **Current conversation only** — Analyze only this session's patterns and feedback

Store the answer as the `scope` for the rest of the skill.

## Phase 1 & 2: Discovery + History Scan (Background, Parallel)

**If scope = "Historical + current conversation":** Launch ALL agents in background simultaneously, then immediately proceed to Phase 3.

**If scope = "Current conversation only":** Launch only the Discovery Agent in background, skip History Scan and Prior-Improve Cross-Check agents entirely, then immediately proceed to Phase 3. Announce: "Launching discovery agent (current conversation scope)."

### Discovery Agent (Explore, background — always runs)

Prompt the agent to search for and catalog ALL config-like files at BOTH project and global levels:
- CLAUDE.md (project root, project .claude/, AND global ~/.claude/CLAUDE.md)
- .claude/commands/ and .claude/skills/ directories (BOTH project-level AND global ~/.claude/commands/)
- .claude/agents/ directory
- .claude/rules/ directory (project-level)
- Shared frameworks, guardrails, style guides (shared/, frameworks/, etc.)
- Memory files at BOTH ~/.claude/projects/[project-path]/memory/ AND ~/.claude/projects/-Users-terence/memory/ (global)
- Settings files: project .claude/settings.json, .claude/settings.local.json, global ~/.claude/settings.json, ~/.claude/settings.local.json
- Voice/brand files (vault/, brand/, etc.)
- Any other instruction-like .md files governing behavior

Return a "config map": list of files with purpose, organized by type AND level (project vs global).

### History Scan Agent (general-purpose, background — full scope only)

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

### Prior-Improve Cross-Check Agent (general-purpose, background — full scope only)

Launch this as a 3rd background agent in parallel with Discovery and History Scan. Its job: audit what prior `/improve` runs recommended and whether their accepted changes actually landed.

For each session file identified by History Scan, check if `/improve` was invoked in that session (`grep -l "/improve"` on the .jsonl). For every session where it was:

1. **Extract the "Changes Applied" summary table** (the final markdown table that `/improve` prints in Phase 6). Or if no table is present, parse the AskUserQuestion responses for Accept/Reject/Modify decisions on each recommendation. Capture: recommendation text, target file, decision.

2. **For each Accepted recommendation**, verify the proposed change actually landed:
   - Read the target file mentioned in the recommendation.
   - Grep for the key phrase / rule text that was supposed to be added.
   - Mark as **Verified Implemented** (key text present), **Drifted** (file exists but text missing or modified), or **Missing** (target file doesn't exist).

3. **For each Skipped / Rejected recommendation**, flag for re-surfacing:
   - Original date + session ID
   - What was recommended and why declined (if captured from user's notes)
   - Whether the underlying friction has recurred since (cross-reference with current History Scan signals)

Return a structured report:
- **Prior `/improve` runs:** N (list dates)
- **Verified implemented:** X (no re-action needed — surface to user for confidence/audit trail)
- **Accepted but drifted/missing:** Y (needs re-application)
- **Previously skipped but still signaling:** Z (re-surface as current-run findings)

Keep total output under 400 words. Cite session dates and target files.

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

For each existing rule in config files, check if the current conversation shows it being violated.

- If the rule was violated once: suggest strengthening (emphasis, position, examples) — not removal
- If the rule shows a pattern of repeated violation (across sessions in full scope, OR multiple times within the current conversation in current-only scope): suggest **converting to a hook** instead — hooks are deterministic enforcement, while CLAUDE.md instructions are probabilistic (~80% compliance)

When presenting enforcement gap findings in Phase 5, offer three options:
- "Strengthen rule" — rewrite with NEVER/ALWAYS emphasis, move to top of file
- "Convert to hook" — create a hook that enforces the rule deterministically. Follow up with a second AskUserQuestion: "Which scope should this hook be configured at?" with options: "Project (.claude/settings.json)", "Global (~/.claude/settings.json)", "Project local (.claude/settings.local.json)"
- "Both" — strengthen the rule AND add a hook as backup enforcement

### 4b: Progressive Evolution (Pattern Promotion) — full scope only

**Skip this sub-phase entirely in current-only scope** (requires cross-session history).

When history scan shows the same feedback across 2+ sessions, suggest PROMOTING:
- Memory file → CLAUDE.md rule
- Buried rule → top of CLAUDE.md with NEVER/ALWAYS emphasis
- Implicit pattern → explicit documented rule with examples
- Soft guideline → hard rule with enforcement language

### 4c: Config Health & Consolidation

Run ALL sub-checks against BOTH project-level and global-level configs from the Discovery Agent config map.

#### Size Thresholds
Measure CLAUDE.md (both project and global) line count and character count.
- **Warning:** >100 lines or >20K characters
- **Critical:** >150 lines or >40K characters (known performance degradation point)
- Also count total memory files — flag if >20 files in a single project's memory directory

#### Memory Consolidation
- Read all memory files and group by topic similarity
- Flag duplicates or heavily overlapping files (e.g., two feedback files covering the same rule) — recommend merging
- Flag memory files with stale references: files, functions, or features mentioned in the memory that no longer exist in the codebase
- Flag memory files with relative dates that were never converted to absolute

#### Rule Extraction
- Scan CLAUDE.md for file-type-specific or path-specific instructions (patterns like "for *.test.ts files", "in API routes", "when editing components/", etc.)
- Suggest migrating these to `.claude/rules/` with path-scoping globs in frontmatter
- Rules only load when Claude touches matching files, reducing always-on context cost

#### Skill Extraction
- Flag CLAUDE.md sections longer than ~20 lines that read like procedures or multi-step workflows
- Suggest converting to skills (on-demand loading: ~100 tokens metadata cost vs full content always in context)
- Good candidates: step-by-step processes, detailed how-to instructions, decision trees

#### Skill Consolidation
- Check ALL skills at both project (`<project>/.claude/commands/`, `<project>/.claude/skills/`) and global (`~/.claude/commands/`) levels
- Flag overlapping skills: two skills that cover similar functionality or could be merged
- Flag oversized skills: skills that have grown beyond their original purpose
- Flag stale skills: skills referencing files, APIs, or patterns that no longer exist in the codebase
- Flag shadowed skills: a project skill with the same name as a global skill (intentional override or accidental?)

#### Cross-Level Analysis
- Check for duplicated rules between project and global CLAUDE.md
- Flag contradictory instructions across levels (project rule says X, global rule says Y)
- Flag memory files that belong at the other level (e.g., project-specific feedback stored in global memory, or cross-project feedback stored in project memory)
- Flag skills that exist at both levels with different content

#### Contradictions
- Two skills/rules giving conflicting instructions within the same level?
- CLAUDE.md rule contradicted by a skill or .claude/rules/ file?

#### Structure
- Files grown organically without clear organization?
- Sections in CLAUDE.md that belong in different files?

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

**FIRST — Audit of Prior `/improve` Runs (full scope only)**

**Skip this section entirely in current-only scope.** Go straight to presenting findings.

In full scope, before presenting any new findings, surface the Prior-Improve Cross-Check report as an audit trail:

```
## Audit of Prior /improve Runs

| Date | Recommendations | Implemented | Drifted | Skipped |
|------|----------------|-------------|---------|---------|
| 2026-04-11 | 6 | 6 ✅ | 0 | 0 |
| 2026-04-09 | 5 | 3 ✅ | 1 ⚠️ | 1 (re-surfaced below) |
```

This gives the user confidence (verified-implemented), highlights drift (needs re-application), and re-surfaces previously-skipped items as new findings to reconsider. NEVER silently drop prior findings — always show the verification.

**THEN — Present Each Finding via AskUserQuestion**

**Question format:** "[Tier] — [Source: current conversation / past session date] — [Description of finding and proposed change]. File: [full path]. Proposed: [what to add/modify/remove]"

**Options:** Accept / Reject / Modify

**Order:**
1. Drifted items (prior accept didn't land — needs re-application)
2. Re-surfaced previously-skipped items (with note: "previously skipped on [date]")
3. Targeted (from /improve args)
4. Critical → Promotion → Improvement → Technique → Maintenance → Reinforcement → New Skill → User Coaching

If 8+ findings, after presenting 5, ask: "Continue with remaining findings, or apply what we have so far?"

## Phase 6: Apply Changes

**Announce:** "Applying N approved changes across M files..."

1. Group approved changes by file
2. Edit existing files with approved modifications
3. Create new files if needed (new memory entries, new skill stubs)
4. For hook conversions:
   - Read the target settings.json file (project or global, per user's scope choice)
   - Add the hook configuration under the appropriate event key (PreToolUse, PostToolUse, etc.)
   - If the hooks key doesn't exist yet, create it
   - Preserve all existing hooks — append, never replace
5. For rule extractions:
   - Create the `.claude/rules/` directory if it doesn't exist
   - Write the extracted rule to a new `.md` file with path-scoping glob in frontmatter
   - Remove the extracted section from CLAUDE.md
6. For memory file merges:
   - Combine the content of overlapping memory files into one
   - Update the frontmatter (name, description) to reflect the merged scope
   - Delete the duplicate file
   - Update MEMORY.md index to remove the deleted entry and update the surviving entry
7. For skill extractions:
   - Create the new skill `.md` file with proper frontmatter (name, description)
   - Move the procedural content from CLAUDE.md into the skill
   - Replace the CLAUDE.md section with a one-line reference: "See /skill-name for details"
8. For feedback-type findings, ALSO save as memory files:
   - File: `feedback_[topic].md` in project's memory directory
   - Frontmatter: name, description, type: feedback
   - Content: rule + **Why:** + **How to apply:**
9. Update MEMORY.md index if new memory files created
10. Present a summary table in the conversation:

    ## Changes Applied

    | # | File | Change | Category |
    |---|------|--------|----------|
    | 1 | path/to/file.md | Brief description of what changed | Category |

    N changes across M files.

    - **File**: short relative path (not full absolute)
    - **Change**: concise action (e.g. "Added rule: …", "Strengthened: X → Y", "New memory: …", "Hook added: …", "Rule extracted: …", "Memory merged: …", "Skill created: …")
    - **Category**: tier from Phase 4d (Critical, Promotion, Improvement, etc.)
11. Ask if user wants to commit changes
