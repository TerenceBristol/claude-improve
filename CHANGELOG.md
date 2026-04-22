# Changelog

All notable changes to the `/improve` skill are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/).

## [3.0.0] - 2026-04-22

### Added
- **Self-learning loop**: Reads `~/.claude/improve-learnings.md` before each run and writes acceptance patterns after. The skill adapts over time — deprioritizing finding types you consistently reject and boosting what you accept.
- **Bidirectional content placement audit** with 5 migration directions:
  - CLAUDE.md → Skill files (skill-specific instructions)
  - Memory → Skills (procedural knowledge in memory)
  - Skill files → CLAUDE.md (universal rules buried in skills)
  - CLAUDE.md → Memory (factual content consuming instruction budget)
  - Between skills (duplicated sections)
- **Cross-skill consistency checking**: Detects contradictions across skill files — conflicting directives, overlapping triggers, inconsistent terminology, process conflicts, and skills-vs-CLAUDE.md mismatches
- **Skill budget monitoring**: Tracks total skill description character count against the community-discovered ~16K metadata budget. Warns when approaching the limit where skills become silently invisible.
- **Skill description quality audit**: Checks each skill's description for activation best practices — third-person voice, trigger conditions, appropriate length, specificity
- **CLAUDE.md structural validation**: Light-touch check against the WHAT/WHY/HOW framework
- **Confidence scoring** on all findings (High / Medium / Low) based on signal strength and cross-session recurrence
- **Smarter hook recommendations**: Generates complete hook JSON config with event type detection, ready to paste into settings.json. Includes compliance improvement estimate.
- **Agent failure graceful degradation**: Discovery Agent falls back to hardcoded config paths; History/Prior-Improve agents degrade to current-only scope. Always announces what was skipped.
- **Content Misplacement** as a new finding category (priority 4th, between Promotion and Improvement)

### Changed
- Phase 5 finding format now includes confidence level: `[Tier | Confidence]`
- Phase 6 expanded with application steps for content moves, skill description rewrites, and learnings file updates
- Priority system now has 10 tiers (added Content Misplacement)
- Removed redundant "Contradictions" sub-section (now covered by Cross-Skill Consistency with 5 specific patterns)

## [2.0.0] - 2026-04-14

### Added
- **Scope selection** — choose "Historical + current conversation" or "Current conversation only" before launch, so you can run a quick current-session-only retrospective without waiting for the full history scan
- **8 structured config health sub-checks** (replacing the previous 4 vague bullets):
  - Size thresholds — warns at >100 lines / >20K chars, critical at >150 lines / >40K chars
  - Memory consolidation — detects duplicate/overlapping memory files, stale references, unconverted relative dates
  - Rule extraction — suggests moving file-type-specific CLAUDE.md rules to `.claude/rules/` with path-scoping
  - Skill extraction — flags CLAUDE.md sections >20 lines that should become on-demand skills
  - Skill consolidation — checks all skills (project + global) for overlapping, stale, oversized, or shadowed skills
  - Cross-level analysis — detects duplicates and contradictions between project and global configs
  - Contradictions — within-level conflicts between skills, rules, and CLAUDE.md
  - Structure — organic growth without clear organization
- **Hook promotion** for enforcement gaps — when rules are repeatedly violated, offers "Strengthen rule", "Convert to hook", or "Both", with a follow-up question for hook scope (project, global, or project-local)
- **4 new change types** in Phase 6: hook creation in settings.json, `.claude/rules/` file creation, memory file merging, and skill extraction from CLAUDE.md
- **Prior /improve audit** — shows a verification table of what past `/improve` runs recommended and whether changes actually landed (full scope only)

### Changed
- Discovery Agent now catalogs both project-level AND global-level configs, including settings files and `.claude/rules/`
- Phase 4a offers 3 response options (Strengthen / Convert to hook / Both) instead of just strengthening
- Phase 4b (Progressive Evolution) is now skipped in current-only scope
- Phase 5 audit table is now skipped in current-only scope
- Phase 6 steps renumbered (1-11) to accommodate new change types

## [1.0.0] - 2026-04-05

### Added
- Initial release of the `/improve` retrospective skill
- 6-phase analysis cycle: Discovery, History Scan, Live Analysis, Cross-Reference, Present Findings, Apply Changes
- 3 parallel background agents (Discovery, History Scan, Prior-Improve Cross-Check)
- 9 signal detection categories: corrections, praise, friction, capability gaps, behavioral patterns, targeted feedback, workflow preferences, techniques, user interaction
- 9-tier priority system (Targeted → Critical → Promotion → Improvement → Technique → Maintenance → Reinforcement → New Skill → User Coaching)
- Rule-writing quality standards (NEVER/ALWAYS emphasis, lead with WHY, concrete examples)
- One-at-a-time finding presentation with Accept/Reject/Modify options
- Automatic memory file creation for feedback-type findings
- Summary table of all applied changes
