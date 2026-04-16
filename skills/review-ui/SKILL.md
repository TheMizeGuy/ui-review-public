---
name: review-ui
description: |-
  Review any UI for quality issues -- visual defects, alignment, spacing, typography, color, responsiveness, motion, accessibility, performance, and anti-patterns. Works across web, iOS, Android, desktop, and screenshots. Standard mode dispatches 2-3 specialists based on scope and platform. For the full 5-specialist + verifier team pass, use review-ui-full.

  Triggers: "review this UI", "check the UI quality", "review my screen", "is this good quality", "check alignment", "review the design", "audit this interface", "does this look right", "check my layout", "review for quality issues", "find UI problems", "review-ui".
argument-hint: '[path | file | directory | screenshot | "diff" | "staged" | "pr" | "all"]'
allowed-tools: Bash, Read, Grep, Glob, TodoWrite, Agent
---

# Review UI

You are coordinating a UI quality review. Determine scope, gather context, dispatch 2-3 specialists, and present a merged report.

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

Dispatch all chosen specialists in parallel using the Agent tool. Send multiple Agent calls in a single message:

```
// Dispatch all in parallel -- one message, multiple tool calls
Agent({
  subagent_type: "ui-review:ui-visual-reviewer",
  model: "opus",
  prompt: "<scope, platform, context, evidence availability>",
  description: "Visual quality review"
})

Agent({
  subagent_type: "ui-review:ui-accessibility-reviewer",
  model: "opus",
  prompt: "<scope, platform, context, evidence availability>",
  description: "Accessibility review"
})

Agent({
  subagent_type: "ui-review:ui-responsive-reviewer",
  model: "opus",
  prompt: "<scope, platform, context, evidence availability>",
  description: "Responsive review"
})
```

## Step 7: Merge and present report

1. Collect findings from all agents
2. Deduplicate (same element, same issue = keep more specific)
3. Re-rank by severity (CRITICAL first)
4. Number sequentially

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
