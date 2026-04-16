---
name: review-ui-full
description: |-
  Comprehensive 5-specialist + verifier UI review. Dispatches all review agents (visual, responsive, motion, accessibility, runtime) plus verification pass for maximum coverage. Use when the user wants the full treatment across all quality dimensions. For standard 2-3 specialist review, use review-ui.

  Triggers: "full UI review", "comprehensive UI review", "review everything about this UI", "thorough UI audit", "full quality audit", "review-ui-full", "complete UI review".
argument-hint: '[path | file | directory | screenshot | "diff" | "staged" | "pr" | "all"]'
allowed-tools: Bash, Read, Grep, Glob, TodoWrite, Agent
---

# Full UI Review

You are coordinating a comprehensive 5-specialist + verifier UI quality review via the team lead.

## Step 1: Determine scope

Same as `review-ui` -- resolve the argument to a concrete file or evidence list.

| Argument | Meaning |
|---|---|
| (empty) or `diff` | Uncommitted + staged changes, filtered to UI-relevant files |
| `staged` | Only staged files |
| `pr` | Diff vs `main`/`master` |
| `<file>` | Single file |
| `<directory>` | All UI files in dir |
| `<screenshot>` | Screenshot file(s) for screenshot-only review |
| `all` | Entire project |

## Step 2: Gather context

Same pre-flight as `review-ui`:
- Detect platform (web/iOS/Android/screenshot-only)
- Gather project context (framework, tokens, config)
- Determine evidence availability (Playwright, code, screenshots)

## Step 3: Dispatch the team lead

The full review dispatches the `ui-review-lead` team lead agent, which internally dispatches all 5 specialists + verifier.

```
Agent({
  subagent_type: "ui-review:ui-review-lead",
  model: "opus",
  prompt: "<scope, platform, project context, evidence availability>",
  description: "Full UI quality review (5 specialists + verifier)"
})
```

The team lead handles:
1. Dispatching visual + responsive + accessibility in parallel
2. Dispatching motion + runtime in parallel
3. Running the verifier on all findings
4. Merging into the unified report with per-dimension verdicts

## Step 4: Present the report

The team lead returns a structured report. Present it verbatim to the user:

```
## UI Quality Review Report

**Scope:** <files/screenshots/pages>
**Platform:** <web / iOS / Android / cross-platform / screenshot-only>
**Review mode:** full (5 specialists + verifier)
**Evidence level:** <code + browser / code-only / screenshot-only>

### Dimension Verdicts

| Dimension | Verdict | Blockers | Key finding |
|---|---|---|---|
| Visual quality | ... | ... | ... |
| Responsive quality | ... | ... | ... |
| Motion quality | ... | ... | ... |
| Accessibility | ... | ... | ... |
| Runtime smoothness | ... | ... | ... |

### Summary

**Findings:** N CRITICAL, N HIGH, N MEDIUM, N LOW, N TASTE
**Verdict:** <ship / fix blockers / needs pass / rework>

### All Findings

[numbered list]

### Verification Notes

[removed/downgraded/merged findings with explanation]
```

## When to use this vs review-ui

| Use `review-ui` when | Use `review-ui-full` when |
|---|---|
| Quick quality check | Pre-launch quality gate |
| Specific concern (alignment, a11y) | Comprehensive audit |
| Small scope (1-5 files) | Large scope or full project |
| Standard review | Maximum coverage needed |
| Cost/time matters | Quality is the priority |
