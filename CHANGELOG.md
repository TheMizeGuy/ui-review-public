# Changelog

All notable changes to the `ui-review` plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.1] - 2026-07-05

### Changed

- Removed every `model:` pin from agent frontmatter and dispatch examples. All seven agents
  (`ui-visual-reviewer`, `ui-responsive-reviewer`, `ui-motion-reviewer`,
  `ui-accessibility-reviewer`, `ui-runtime-reviewer`, `ui-verifier`, `ui-review-lead`) now
  inherit the session model -- always the strongest available Claude -- rather than requiring
  a specific named model.
- Added "Execution mode" notes to both skills: dispatching stays the default for
  multi-specialist parallel reviews, but for small scopes on an already-top-tier session the
  coordinator may run a review inline instead, without weakening the read-only guarantee or
  skipping the verifier pass in the full-review workflow.
- Anti-pattern catalogue corrected from 148 to its actual total of 153 patterns, and now
  cross-references the `typescript-ui` 108-tell catalogue and `anti-slop` design/frontend
  pattern catalogues as optional companions.
- Added the canonical **geometry evidence rule** to `references/09-evidence-pipeline.md` as
  the single source of truth for when a spatial or numeric-precision claim (alignment,
  distances, target sizes, overlap, contrast ratios) requires real geometry data instead of
  screenshot estimation. Every agent and both skills now cite this one rule rather than
  restating it.
- Added a fully worked, evidence-complete viewport finding to
  `agents/ui-responsive-reviewer.md` demonstrating the strict finding format with all three
  geometry contexts (viewport, container, component box) satisfied.
- Added acceptance-criteria / pre-present checklists to both skills so a report is checked
  against concrete pass conditions (finding format, geometry rule, ordering, coverage
  disclosure) before being shown to the user.
- `plugin.json` author now carries a contact email; the `opus` keyword was dropped now that
  agents no longer pin a specific model.

### Added

- `USAGE.md` -- quickstart plus a full walkthrough of `review-ui` and `review-ui-full`, and a
  troubleshooting table.
- This changelog.

## [1.0.0] - 2026-04-16

### Added

- Initial public release of the `ui-review` plugin.
- Seven specialist agents (visual, responsive, motion, accessibility, runtime, verifier, team
  lead) backed by 10 self-contained reference files (universal rubric, anti-pattern catalogue,
  viewport matrix, motion heuristics, accessibility checklist, web/Apple/Material overlays,
  evidence pipeline, typography/color/layout foundations).
- Two user-invoked skills: `review-ui` (standard 2-3 specialist pass) and `review-ui-full`
  (comprehensive 5-specialist + verifier team pass via `ui-review-lead`).
- Severity-tagged findings (CRITICAL/HIGH/MEDIUM/LOW/TASTE) with confidence classes (Hard
  defect/Quality defect/Pattern smell/Taste note), evidence citations, and concrete
  remediation. Read-only by default.
- MIT license.

[1.0.1]: https://github.com/TheMizeGuy/ui-review-public/releases/tag/v1.0.1
[1.0.0]: https://github.com/TheMizeGuy/ui-review-public/releases/tag/v1.0.0
