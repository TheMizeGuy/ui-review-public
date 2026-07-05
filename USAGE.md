# ui-review -- Usage Guide

A walkthrough of each skill: what to type, what happens, and what you get back. For the
specialist/reference file map, see [`README.md`](README.md).

## Quickstart

1. **Install** -- clone the repo and point Claude Code at it (see
   [Installation](README.md#installation) in the README).
2. **First invocation** -- from any project with UI code (or just a screenshot on disk), run:
   ```
   /ui-review:review-ui
   ```
   With no argument, this reviews your uncommitted + staged changes filtered to UI-relevant
   files. Pass a path, a screenshot, `staged`, `pr`, or `all` to target something else.
3. **What to expect** -- the skill detects the platform (web/iOS/Android/screenshot-only),
   picks 2-3 specialists based on scope, dispatches them in parallel, merges their findings,
   and presents one numbered report ordered CRITICAL first. Nothing on disk is ever modified --
   every finding is advisory.

## Walkthrough: `/ui-review:review-ui` -- standard review

**You type:**
```
/ui-review:review-ui src/components/CheckoutForm.tsx
```

**What happens:**

1. The skill resolves the scope to that one file and detects the platform from surrounding
   project files (`package.json` with react/vue/svelte/next → Web; `*.swift` with
   `import SwiftUI` → iOS/Apple; `build.gradle` or Compose `*.kt` → Android; screenshots only →
   screenshot-only mode).
2. It gathers project context in parallel -- for web, that's the framework, `tailwind.config.*`
   or `globals.css` token system, and `tsconfig.json` strictness.
3. It decides which specialists to dispatch. A web component always gets
   `ui-visual-reviewer` and `ui-accessibility-reviewer`; web also defaults in
   `ui-responsive-reviewer` (3 agents total). Add `ui-motion-reviewer` if animation is in
   scope, or `ui-runtime-reviewer` if performance is a stated concern.
4. All chosen specialists run in parallel, each reading its own reference files
   (`01-universal-rubric.md`, the anti-pattern catalogue, the evidence pipeline, plus the
   relevant platform overlay) before reviewing.
5. The skill merges results itself (there is no dedicated verifier agent in standard mode): it
   deduplicates using the verifier's dedup/grouping rules, validates severities (taste never at
   HIGH/CRITICAL; accessibility blockers never below HIGH), caps any spatial claim lacking
   geometry evidence at "Possible issue -- geometry measurement needed", then re-ranks and
   numbers the findings.

**You get:**

```
## UI Quality Review

**Scope:** src/components/CheckoutForm.tsx
**Platform:** Web
**Specialists:** ui-visual-reviewer, ui-accessibility-reviewer, ui-responsive-reviewer
**Evidence level:** code-only

**Findings:** 0 CRITICAL, 1 HIGH, 2 MEDIUM, 1 LOW, 1 TASTE
**Verdict:** fix the HIGH before shipping

1. [HIGH] [Hard defect] Accessibility -- submit button has no accessible name
   ...
```

Every finding carries a severity tag, a confidence class, the affected surface, why it
matters, the evidence behind it, and a recommended fix. If a dimension wasn't reviewed
(no Playwright, screenshot-only scope, etc.), the report header says so explicitly.

## Walkthrough: `/ui-review:review-ui-full` -- comprehensive review

**You type:**
```
/ui-review:review-ui-full all
```

**What happens:**

1. Same scope resolution and platform/context detection as `review-ui`, but `all` reviews the
   entire project (excluding `node_modules`, `dist`, `.build`, `Pods`, `DerivedData`).
2. Instead of dispatching specialists directly, the skill dispatches the `ui-review-lead`
   orchestrator. Because plugin-namespaced dispatch strips the `Agent` tool a sub-orchestrator
   needs, this happens via `general-purpose` with `ui-review-lead`'s full agent body inlined as
   the prompt prefix -- not via the plugin namespace.
3. The lead dispatches all 5 specialists (visual, responsive, accessibility in one wave; motion,
   runtime in another), then runs `ui-verifier` against the combined findings -- checking
   evidence sufficiency, removing false positives, downgrading unsupported spatial claims, and
   deduplicating across dimensions.
4. The lead merges everything into a report with a per-dimension verdict table, so an
   accessibility gap can never get buried under an otherwise-strong visual score.

**You get:**

```
## UI Quality Review Report

**Scope:** entire project (142 files)
**Platform:** Web
**Review mode:** full (5 specialists + verifier)
**Evidence level:** code + browser

### Dimension Verdicts

| Dimension | Verdict | Blockers | Key finding |
|---|---|---|---|
| Visual quality | ADEQUATE | 0 | Inconsistent card radii across 3 components |
| Responsive quality | FRAGILE | 1 | Checkout summary clips the pay button below 430px |
| Motion quality | ADEQUATE | 0 | Modal transition ignores prefers-reduced-motion |
| Accessibility | GAPS | 2 | Two icon-only buttons have no accessible name |
| Runtime smoothness | ACCEPTABLE | 0 | Hero image not responsive-sized |

### Summary
**Findings:** 1 CRITICAL, 3 HIGH, 5 MEDIUM, 4 LOW, 2 TASTE
**Verdict:** fix blockers before shipping

### All Findings
[numbered list, CRITICAL first, TASTE last]

### Verification Notes
[removed/downgraded/merged findings with explanation, or "none"]
```

Use `review-ui-full` for pre-launch quality gates or full-project audits; use `review-ui` for
a quick, targeted check where dispatching the team-lead's extra coordination overhead isn't
worth it.

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| "Screenshot-only review covers visual quality and estimated accessibility..." message, fewer specialists than expected | Only screenshot files matched scope -- no code, no running app | Expected behavior. Point the skill at source files or a running app to unlock responsive, motion, and runtime review |
| A finding says "Possible issue -- geometry measurement needed" instead of a firm severity | No DOM bounding box / element frame / layout-bounds data was available for that spatial claim (the canonical geometry evidence rule in `references/09-evidence-pipeline.md`) | Provide Playwright MCP (web) or a running app with an accessibility-tree/hierarchy snapshot so the specialist can measure instead of estimate |
| `review-ui-full` seems to hang or the team lead never dispatches its 5 specialists | The lead was invoked via the plugin namespace (`ui-review:ui-review-lead`), which silently strips the `Agent` tool it needs | Dispatch via `general-purpose` with the lead's full agent body inlined, per Step 3 of `skills/review-ui-full/SKILL.md` |
| Report is missing a dimension you expected (e.g. no motion findings on a static page) | `review-ui` only dispatches 2-3 specialists by default; motion and runtime are conditional on scope | Ask explicitly, or use `review-ui-full` to force all 5 specialists |
| Findings reference `typescript-ui` or `anti-slop` catalogues you don't have installed | `02-anti-pattern-catalogue.md` cross-references those plugins' deeper catalogues when present | Optional -- install those plugins for extra AI-tell coverage, or ignore; the base 153-pattern catalogue works standalone |
| "Entire project" (`all`) scope times out or reviews too much | No path filter applied; large repos exceed the >50-file warning threshold | Scope to a directory or `diff`/`staged`/`pr` instead of `all` on large repos |
