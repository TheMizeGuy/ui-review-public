# ui-review

Universal UI review plugin for Claude Code. Reviews any UI surface -- web, iOS, Android, web-based desktop (Electron/Tauri), design files, screenshots -- for visual quality, alignment, spacing, typography, color, responsive behavior, motion, accessibility, runtime performance, and anti-pattern detection.

Seven specialist agents, each running on the session model (always the strongest available Claude), backed by 10 self-contained reference files, produce severity-tagged findings with evidence citations, confidence classes, and concrete remediation. Read-only by default.

See [`USAGE.md`](USAGE.md) for a quickstart and a worked walkthrough of each skill.

## What you get

| Specialist | Focus |
|---|---|
| `ui-visual-reviewer` | Layout, spacing, typography, color, hierarchy, component coherence, anti-patterns |
| `ui-responsive-reviewer` | Viewports, overflow, clipping, safe areas, reflow, adaptive layout |
| `ui-motion-reviewer` | Animation timing, purpose, reduced motion, compositor-safe properties |
| `ui-accessibility-reviewer` | Semantics, keyboard, focus, contrast, targets, WCAG 2.2, APCA |
| `ui-runtime-reviewer` | Core Web Vitals (LCP/INP/CLS), jank, heavy effects, image/font optimization |
| `ui-verifier` | Evidence check, false-positive filter, deduplication, severity validation |
| `ui-review-lead` | Team-mode orchestrator for the full 5-specialist + verifier pass |

## Skills

| Skill | When |
|---|---|
| `review-ui` | Standard review. Dispatches 2-3 specialists based on scope/platform. |
| `review-ui-full` | Comprehensive review. All 5 specialists + verifier via the team lead. |

## Reference library

10 self-contained reference files (153 anti-patterns, cross-platform overlays, severity scale, evidence pipeline):

| File | Content |
|---|---|
| `01-universal-rubric.md` | 9 review dimensions, platform overlays, severity scale, confidence classes, finding format |
| `02-anti-pattern-catalogue.md` | 153 catalogued patterns: color (17), typography (12), layout (12), components (12), copy (10), motion (7), images (6), micro (12), deep cuts (20), visual (10), platform-specific (35). Cross-references the `typescript-ui` 108-tell catalogue and `anti-slop` design/frontend patterns when those plugins are installed |
| `03-viewport-matrix.md` | Web/iOS/Android viewport families, failure classes, de-duplication rules |
| `04-motion-heuristics.md` | Good vs bad motion, spring physics reference, reduced-motion implementation, evidence requirements |
| `05-accessibility-checklist.md` | WCAG 2.2, APCA targets, screen reader patterns, focus/touch reference, media queries |
| `06-web-overlay.md` | CSS architecture, modern layout, typography, OKLCH color, CWV thresholds, frameworks |
| `07-apple-overlay.md` | Clarity/Deference/Depth, Liquid Glass, SF Symbols, Dynamic Type, haptics, adaptive iPad |
| `08-material-android-overlay.md` | Window size classes, adaptive layout, Compose accessibility, Material 3, configuration changes |
| `09-evidence-pipeline.md` | Artifact bundle schema, capture strategies, evidence rules by claim type, confidence calibration |
| `10-typography-color-layout.md` | Type scale, OKLCH tokens, dark mode engineering, contrast standards, grid/spacing/alignment |

## Severity and confidence

Every finding carries both a severity tag (CRITICAL / HIGH / MEDIUM / LOW / TASTE) and a confidence class (Hard defect / Quality defect / Pattern smell / Taste note). A beautiful but inaccessible UI is broken: accessibility blockers on core paths rank above all visual polish findings.

## Usage

Invoke with a scope argument:

```
/ui-review:review-ui [path | file | directory | screenshot | "diff" | "staged" | "pr" | "all"]
/ui-review:review-ui-full [path | file | directory | screenshot | "diff" | "staged" | "pr" | "all"]
```

| Argument | Meaning |
|---|---|
| empty / `diff` | Uncommitted + staged changes, filtered to UI-relevant files |
| `staged` | Only staged files |
| `pr` | Diff vs `main`/`master` |
| `<file>` or `<directory>` | Specific target |
| `<screenshot>` | Screenshot file(s), screenshot-only review |
| `all` | Entire project (excl. node_modules, dist, .build, Pods, DerivedData) |

Findings are always advisory. The plugin never modifies files.

## Platform detection

Auto-detected from the scope:

| Signal | Platform |
|---|---|
| `package.json` with react/vue/svelte/next | Web |
| `*.swift` with `import SwiftUI` | iOS/Apple |
| `build.gradle` or `*.kt` with `@Composable` | Android |
| `*.html` / `*.css` only | Web (static) |
| Screenshot files only | Screenshot-only mode (lower confidence on geometry claims) |

## Hard rules

1. **Read-only** -- findings are advisory, never modifies files.
2. **Cite references** -- every finding traces back to a rubric dimension or checklist item.
3. **Evidence-backed** -- no pixel-precision claims without geometry data.
4. **Severity discipline** -- never escalate taste into CRITICAL or HIGH.
5. **Verify before reporting** -- full reviews always include the verifier pass.
6. **Separate verdicts** -- per-dimension verdicts prevent burying accessibility in a visual score.
7. **Platform-appropriate** -- don't enforce Apple patterns on Android or vice versa.
8. **Quality over style** -- a distinctive interface that breaks conventions for a reason scores better than a generic but clean template.

## Requirements

- Claude Code with plugin support. Agents run on the session model -- always the strongest available Claude -- so review depth follows whatever the session already runs at, with no separate model to provision.
- Optional: Playwright MCP for web evidence capture (screenshots, DOM, a11y scans, traces)
- Optional: Serena MCP for semantic code navigation
- Optional: Context7 MCP for framework/library API verification
- Optional: GoodMem MCP for cross-run learnings (prior findings, recurring patterns); fill in your own space and reranker IDs if you wire this up
- Optional companions cross-referenced from the anti-pattern catalogue: `typescript-ui` (108-tell catalogue) and `anti-slop` (design/frontend pattern catalogues) -- neither is a dependency

## Installation

```bash
# 1. Add this repo as a marketplace
claude plugin marketplace add https://github.com/TheMizeGuy/ui-review-public.git

# 2. Install the plugin
claude plugin install ui-review@ui-review-public

# 3. Restart Claude Code for the plugin to load
```

After restart, verify with `claude plugin list`. Updates ship through the same channel: when a new release lands, run `claude plugin marketplace update ui-review-public` then `claude plugin update ui-review@ui-review-public`, or accept the update prompt in `/plugin`.

Manual alternative: `git clone https://github.com/TheMizeGuy/ui-review-public.git` and load with `claude --plugin-dir <path>`.

## License

MIT
