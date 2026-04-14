# Changelog

All notable changes to the `/improve` skill are documented here.

Format follows [Keep a Changelog](https://keepachangelog.com/).

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
