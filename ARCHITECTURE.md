# ui-review -- Architecture

Internal map of files, responsibilities, and cross-references.

## Mission

A team of specialists, each running on the session model (always the strongest available Claude), that reviews any UI surface -- web, iOS, Android, desktop, design files, screenshots -- for visual quality, alignment, spacing, typography, color, responsive behavior, motion, accessibility, runtime performance, and anti-pattern detection. Evidence-backed findings with severity tags, confidence classes, and concrete remediation. Read-only by default.

## Layout

```
ui-review/
├── .claude-plugin/plugin.json
├── ARCHITECTURE.md                     (this file)
├── skills/                             (2 user-invoked entry points)
│   ├── review-ui/SKILL.md             "review this UI"
│   └── review-ui-full/SKILL.md        "thorough/comprehensive/full UI review"
├── agents/                             (7 specialists, session-model)
│   ├── ui-visual-reviewer.md          layout, spacing, typography, color, hierarchy, anti-patterns
│   ├── ui-responsive-reviewer.md      viewports, overflow, clipping, safe areas, reflow
│   ├── ui-motion-reviewer.md          animation timing, purpose, interruptibility, reduced motion
│   ├── ui-accessibility-reviewer.md   semantics, keyboard, focus, contrast, targets, announcements
│   ├── ui-runtime-reviewer.md         CWV, latency, layout shift, jank, heavy effects
│   ├── ui-verifier.md                 evidence check, false-positive filter, deduplication
│   └── ui-review-lead.md             team-mode orchestrator for full reviews
└── references/                         (10 self-contained knowledge files)
    ├── 01-universal-rubric.md         review dimensions, severity scale, finding format, confidence classes
    ├── 02-anti-pattern-catalogue.md   153 anti-patterns (17 color, 12 typo, 12 layout, 12 component, 10 copy, 7 motion, 6 image, 12 micro, 20 deep cuts, 10 visual, 35 platform) + cross-refs to typescript-ui 108-tell and anti-slop catalogues
    ├── 03-viewport-matrix.md          viewport families, de-duplication rules, mobile/safe-area heuristics
    ├── 04-motion-heuristics.md        good/bad motion, spring physics, reduced motion, evidence requirements
    ├── 05-accessibility-checklist.md  WCAG 2.2, APCA, keyboard/focus, screen reader, touch targets, media queries
    ├── 06-web-overlay.md              web-specific: CSS, layout, typography, color, CWV, frameworks
    ├── 07-apple-overlay.md            Apple-specific: HIG, Liquid Glass, Dynamic Type, SF Symbols, haptics
    ├── 08-material-android-overlay.md Android-specific: adaptive quality, window size classes, Compose, Material 3
    ├── 09-evidence-pipeline.md        artifact bundles, capture strategies, evidence rules, confidence calibration
    └── 10-typography-color-layout.md  design foundations: type, color, spacing, alignment
```

## Skill -> agent mapping

| Skill | Agents dispatched | Mode |
|---|---|---|
| `review-ui` | 2-3 specialists based on scope (always visual + a11y, optionally responsive/motion/runtime) | Parallel, skill merges |
| `review-ui-full` | `ui-review-lead` -> all 5 specialists + verifier | Team lead orchestrates |

## Reference -> agent mapping

| Agent | Reads (must) |
|---|---|
| `ui-visual-reviewer` | 01 (rubric), 02 (anti-patterns), 09 (evidence -- geometry rule), 10 (typography/color/layout) + platform overlay |
| `ui-responsive-reviewer` | 01 (rubric), 03 (viewport matrix), 09 (evidence) + platform overlay |
| `ui-motion-reviewer` | 01 (rubric), 04 (motion heuristics), 09 (evidence) + platform overlay |
| `ui-accessibility-reviewer` | 01 (rubric), 05 (accessibility), 09 (evidence) + platform overlay |
| `ui-runtime-reviewer` | 01 (rubric), 06 (web overlay for CWV), 09 (evidence) |
| `ui-verifier` | 01 (rubric), 09 (evidence) |
| `ui-review-lead` | 01 (rubric), all references (routing, not deep work) |

## External knowledge sources

| Source | What it provides |
|---|---|
| `~/Claude/vault/UI Design/` (12 files) | Deep reference on design principles, typography, color, layout, motion, accessibility, performance, mobile, trends |
| `~/Claude/vault/iOS Development/` (88 files) | Apple platform depth: HIG, SwiftUI, accessibility, animation |
| `~/Claude/vault/Claude Code UI Review Plugin/` (10 files) | Research vault that informed this plugin's design |
| GoodMem Learnings | Prior review findings, debug learnings, API gotchas |
| Context7 | Current framework/library API docs |
| Playwright MCP | Browser automation for web evidence capture |

## Severity scale (shared by all agents)

| Tag | Meaning |
|---|---|
| CRITICAL | Blocks use, accessibility violation that excludes users, core path broken |
| HIGH | Visible degradation, broken reflow, confusing hierarchy, animation impedes use |
| MEDIUM | Quality gap: generic visual system, weak emphasis, awkward spacing, stiff motion |
| LOW | Minor polish: shadow weight, icon consistency, minor rhythm drift |
| TASTE | Non-blocking stylistic suggestion |

## Confidence classes

| Class | Meaning |
|---|---|
| Hard defect | Objective, should be fixed |
| Quality defect | Strongly justified, alternatives exist |
| Pattern smell | Commonly correlated with poor output |
| Taste note | Advisory only |

## Hard rules

1. **Read-only.** Findings are advisory. Never modify files.
2. **Cite references.** Every finding traces to rubric dimension or checklist item.
3. **Evidence-backed.** No pixel-precision claims without geometry data (canonical rule: `references/09-evidence-pipeline.md`, "Geometry evidence rule").
4. **Severity discipline.** Never escalate taste into CRITICAL/HIGH.
5. **Verify before reporting.** Full reviews always include verifier pass.
6. **Separate verdicts.** Per-dimension verdicts prevent burying accessibility in visual score.
7. **No AI slop.** No praise, hedging, or trailing summaries.
8. **Platform-appropriate.** Don't enforce Apple patterns on Android or vice versa.
9. **Quality over style.** Distinctive design > generic template. Broken trendy UI scores poorly.

## Scoring model

### Layer 1: Blocker flags
- `accessibility_blocker`
- `responsive_blocker`
- `core_task_blocker`
- `runtime_instability`

### Layer 2: Per-dimension verdicts

| Dimension | Verdict set |
|---|---|
| Visual quality | STRONG / ADEQUATE / WEAK / BROKEN |
| Responsive quality | ROBUST / ADEQUATE / FRAGILE / BROKEN |
| Motion quality | FLUID / ADEQUATE / STIFF / HARMFUL |
| Accessibility | INCLUSIVE / ADEQUATE / GAPS / EXCLUDING |
| Runtime smoothness | RESPONSIVE / ACCEPTABLE / SLUGGISH / UNSTABLE |

### Layer 3: Optional summary
Only after above. Never replaces blocker flags or verdict table.

## Design principles

Derived from deep research (vault notes 01-09):

1. **Orchestrator + specialists + verifier.** Not a single omniscient reviewer.
2. **Screenshot-only mode stays low-confidence.** Anthropic documents spatial reasoning limits.
3. **Evidence bundles over loose screenshots.** State manifests, viewport manifests, structured metadata.
4. **Objective defects > subjective taste.** Clear separation prevents noise.
5. **Matrix-based responsive review.** Not binary mobile/desktop.
6. **Motion/a11y/performance get separate verdicts.** Non-visual problems don't disappear in a style score.
7. **GoodMem write-back for durable learnings.** Not every nit -- verified recurring patterns only.
