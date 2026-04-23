# Changelog

## v1.0.1 (2026-04-23)

Documentation-only patch. Reflects the arrival of `architecture-ready` v1.0.0 (https://github.com/aihxp/architecture-ready) as a live sibling in the ready-suite. architecture-ready is the top of the planning tier and architecture-ready's upstream. No behavioral changes to the skill.

### Changed

- **`SUITE.md`** updated to list architecture-ready at 1.0.0. Every sibling's recorded version is bumped one patch to track the release. Copy remains byte-identical across every live sibling.
- **SKILL.md frontmatter version** bumped to 1.0.1. No content change beyond the version tag.

All notable changes to this skill are documented here. Format loosely follows [Keep a Changelog](https://keepachangelog.com/). Version numbers follow the ready-suite discipline: major for breaking skill-contract changes, minor for additive behavior changes, patch for documentation-only updates (including sibling-ship tracking in SUITE.md).

## v1.0.0 (2026-04-23)

Initial public release. prd-ready is the top of the planning tier; nothing upstream feeds into it. Four downstream siblings consume its output: architecture-ready (not yet released), roadmap-ready (not yet released), stack-ready (live), production-ready (live).

### Added

- **`SKILL.md`** with the full workflow (Step 0 through Step 12), four completion tiers (Brief, Spec, Full PRD, Launch-Ready), the three-label audit discipline (every sentence is a decision, a flagged hypothesis, or a named open question), and have-nots (invisible PRD, feature laundry list, solution-first PRD, assumption-soup PRD, moving-target PRD, hollow PRD, and more).
- **Six named antipatterns** as load-bearing skill vocabulary: hollow PRD (suite-consistent with production-ready's hollow-button pattern), invisible PRD (Tom Leung, Fireside PM, 2024), feature laundry list (ProductPlan), solution-first PRD (Intercom), moving-target PRD (Horowitz 1998), assumption-soup PRD (coined). Three secondary terms reserved as rhetorical flourishes: theater PRD, quill-and-inkwell PRD, superficial-completion PRD.
- **Eleven reference files** covering the full PRD authoring surface:
  - `prd-research.md`: Step 0 mode detection (Modes A through F).
  - `problem-framing.md`: Step 1 seven pre-flight questions; Step 2 problem-first discipline; substitution test applied to PRDs.
  - `user-personas.md`: Step 3 five-bullet user description replacing fictional personas.
  - `success-criteria.md`: Step 4 four-property rule; leading vs. lagging; HEART framework lens; success-metric handoff to observe-ready.
  - `requirements.md`: Step 5 functional + Step 6 non-functional; MoSCoW discipline; Given-When-Then acceptance criteria.
  - `scope-and-out-of-scope.md`: Step 7 three-part Out-of-Scope; rabbit holes with smallest-version alternatives; appetite discipline.
  - `risks-and-assumptions.md`: Step 8 three-way boundary; owner/due/blocking flag discipline.
  - `prd-anatomy.md`: full annotated PRD template; section order; length conventions; downstream handoff block full template; PR-FAQ optional mode.
  - `prd-antipatterns.md`: the six primary antipatterns; Mode C audit procedure; tier-gate audit; banned-phrase grep list.
  - `stakeholder-alignment.md`: Step 10 sign-off roster by tier; per-role attestations; async sign-off; common disagreements.
  - `iterate-vs-freeze.md`: Step 11 four lifecycle states; change-control protocol; broadcast discipline; when Tier 1 is enough.
- **Downstream handoff block** (`.prd-ready/HANDOFF.md`) with four sub-sections pre-filling architecture-ready, roadmap-ready, stack-ready (v1.1.5 compatible; matches stack-ready's six pre-flight questions), and production-ready (v2.5.6 compatible).
- **Research pass** (`references/RESEARCH-2026-04.md`) with citations from Marty Cagan (SVPG), Ben Horowitz (1998 memo), Lenny Rachitsky, Intercom Product Principles, Basecamp Shape Up (Ryan Singer), Amazon Working Backwards / PR-FAQ (Colin Bryar), Gibson Biddle DHM, Reforge three-stage evolution, Atlassian PRD template, Google design docs, HashiCorp Problem Requirements Document, Adam Fishman (FishmanAF), Aakash Gupta (Mind the Product), Tom Leung (Fireside PM), and others. Named-term survey identifies which failure-mode terms are open-lane vs. taken.
- **`SUITE.md`** byte-identical to every sibling in the ready-suite, updated to list prd-ready v1.0.0 alongside production-ready 2.5.6, stack-ready 1.1.5, repo-ready 1.6.2, deploy-ready 1.0.4, observe-ready 1.0.3, launch-ready 1.0.1.
- **`README.md`** with problem framing, install instructions, trigger list (positive / implied / mode / negative), "What this skill prevents" mapping against documented research, named-terms section.
- **LICENSE** (MIT, matching the suite).
