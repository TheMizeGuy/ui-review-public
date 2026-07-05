---
name: review-ui
description: |-
  Review any UI for quality issues -- visual defects, alignment, spacing, typography, color, responsiveness, motion, accessibility, performance, and anti-patterns. Works across web, iOS, Android, desktop, and screenshots. Standard mode dispatches 2-3 specialists based on scope and platform. For the full 5-specialist + verifier team pass, use review-ui-full.

  Triggers: "review this UI", "check the UI quality", "review my screen", "is this good quality", "check alignment", "review the design", "audit this interface", "does this look right", "check my layout", "review for quality issues", "find UI problems", "review-ui".
argument-hint: '[path | file | directory | screenshot | "diff" | "staged" | "pr" | "all"]'
allowed-tools: Bash, Read, Grep, Glob, TodoWrite, Agent
---

# Review UI

Coordinate a UI quality review: determine scope, gather context, dispatch 2-3 specialists, and present a merged report.

## Step 1: Determine scope

Resolve the argument to a concrete file or evidence list:

| Argument | Meaning |
|---|---|
| (empty) or `diff` | Uncommitted + staged changes, filtered to UI-relevant files |
| `staged` | Only staged files |
| `pr` | Diff vs `main`/`master` |
| `<file>` | Single file |
| `<directory>` | All UI files in dir |
| `<screenshot>` | Screenshot file(s) for screenshot-only review |
| `all` | Entire project (excluding node_modules, dist, build, .build, Pods, DerivedData) |

### UI-relevant file extensions

**Web:** `*.tsx`, `*.jsx`, `*.vue`, `*.svelte`, `*.css`, `*.scss`, `*.html`
**iOS:** `*.swift` (containing SwiftUI views), `*.storyboard`, `*.xib`
**Android:** `*.kt` (containing Compose), `*.xml` (layouts)
**Screenshots:** `*.png`, `*.jpg`, `*.jpeg`, `*.webp`

If empty scope, tell user and suggest alternatives. If >50 files, warn and ask.

## Step 2: Detect platform

| Signal | Platform |
|---|---|
| `package.json` with react/vue/svelte/next | Web |
| `*.swift` with `import SwiftUI` | iOS/Apple |
| `build.gradle` or `*.kt` with `@Composable` | Android |
| `*.html` / `*.css` only | Web (static) |
| Screenshot files only | Screenshot-only mode |

## Step 3: Gather context (parallel)

**Web:**
- `package.json` -- framework, Tailwind, design libraries
- `globals.css` / `tailwind.config.*` -- token system
- `tsconfig.json` -- strictness

**iOS:**
- Asset catalog -- named colors, images
- Project structure -- SwiftUI vs UIKit mix
- Info.plist -- supported orientations, scenes

**Android:**
- `build.gradle` -- min SDK, Material version
- `themes.xml` / `colors.xml` -- design system
- Compose vs XML split

## Step 4: Decide which specialists to dispatch

### Code or browser review

| Condition | Specialists |
|---|---|
| Any review | `ui-review:ui-visual-reviewer` (always) |
| Any review | + `ui-review:ui-accessibility-reviewer` (always) |
| Web platform detected | + `ui-review:ui-responsive-reviewer` (default for web) |
| iOS/Android adaptive layout | + `ui-review:ui-responsive-reviewer` |
| Motion/animation in scope | + `ui-review:ui-motion-reviewer` |
| Performance concern | + `ui-review:ui-runtime-reviewer` |

Default for **web**: visual + accessibility + responsive (3 agents).
Default for **mobile native**: visual + accessibility (2 agents).

### Screenshot-only review

When only screenshots are provided (no code, no running app):

| Specialists | Notes |
|---|---|
| `ui-review:ui-visual-reviewer` | Full visual review on screenshots |
| `ui-review:ui-accessibility-reviewer` | Limited: contrast, target size estimation, semantic guesses. Findings marked lower confidence. |

Do NOT dispatch: responsive (can't resize), motion (can't observe), runtime (can't measure).

Tell the user which dimensions could not be reviewed:
> "Screenshot-only review covers visual quality and estimated accessibility. Responsive behavior, motion quality, and runtime performance require code access or a running application."

## Step 5: Construct prompts

Each agent gets a self-contained prompt with:
- Absolute file paths or screenshot paths
- Platform identification
- Project context (framework, tokens, config)
- Instruction to read their plugin references BEFORE reviewing
- Evidence availability (Playwright available? Code access? Screenshots only?)
- Output in strict finding format from `01-universal-rubric.md`

## Step 6: Dispatch agents in parallel

Dispatch all chosen specialists in parallel using the Agent tool. Send multiple Agent calls in a single message. Omit `model` on each call -- specialists inherit the session model (always the strongest available Claude):

```
// Dispatch all in parallel -- one message, multiple tool calls
Agent({
  subagent_type: "ui-review:ui-visual-reviewer",
  prompt: "<scope, platform, context, evidence availability>",
  description: "Visual quality review"
})

Agent({
  subagent_type: "ui-review:ui-accessibility-reviewer",
  prompt: "<scope, platform, context, evidence availability>",
  description: "Accessibility review"
})

Agent({
  subagent_type: "ui-review:ui-responsive-reviewer",
  prompt: "<scope, platform, context, evidence availability>",
  description: "Responsive review"
})
```

### Execution mode

Specialists inherit the session model. If the session model is already the strongest available tier and scope is small (1-2 files, one dimension), the coordinator may run the review inline in the main context instead of dispatching -- never dispatch to, or wait on, a specific named model. Dispatching stays the default for multi-specialist parallel reviews; inline execution never grants write access -- findings stay advisory and files stay unmodified, exactly as the read-only specialist agents behave.

## Step 7: Merge and present report

Standard mode has no dedicated verifier agent -- the coordinator applies the verifier's rules
during the merge:

1. Collect findings from all agents
2. Deduplicate and group using the "Deduplication rules" and "Grouping" sections of
   `${CLAUDE_PLUGIN_ROOT}/agents/ui-verifier.md` (same element + same issue = keep the more
   specific finding; cross-dimension contrast/clipping/reduced-motion overlaps merge into one)
3. Validate severities against the "Severity validation" table in the same file (taste never
   at HIGH/CRITICAL; accessibility blockers never below HIGH)
4. Enforce the geometry evidence rule (`${CLAUDE_PLUGIN_ROOT}/references/09-evidence-pipeline.md`,
   "Geometry evidence rule"): any spatial claim without geometry evidence is capped at
   "Possible issue -- geometry measurement needed"
5. Re-rank by severity (CRITICAL first) and number sequentially

```
## UI Quality Review

**Scope:** <files, count>
**Platform:** <detected platform>
**Specialists:** <which were dispatched>
**Evidence level:** <code + browser / code-only / screenshot-only>

**Findings:** N CRITICAL, N HIGH, N MEDIUM, N LOW, N TASTE
**Verdict:** <one line>

[numbered findings list]
```

### Before presenting (all must pass)

| Check | Pass condition |
|---|---|
| Finding format | Every finding carries [SEVERITY] [CONFIDENCE] plus Surface, Issue, Why it matters, Evidence, Recommended change |
| Evidence present | No finding with an empty or vague Evidence field ("looks off" fails) |
| Geometry rule | No spatial claim above "Possible issue" confidence without geometry evidence |
| Ordering | Findings numbered sequentially, CRITICAL first, TASTE last |
| Coverage disclosure | Dimensions NOT reviewed (undispatched specialists, screenshot-only limits) named in the report header |

Fix formatting failures during the merge; if a finding's substance is unverifiable, drop it
and record the drop under a "Merge notes" line in the report.

## Step 8: Write learnings

If the review surfaced non-obvious patterns, write to GoodMem Learnings.
