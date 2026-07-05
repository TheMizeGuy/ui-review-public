---
name: review-ui-full
description: |-
  Comprehensive 5-specialist + verifier UI review. Dispatches all review agents (visual, responsive, motion, accessibility, runtime) plus verification pass for maximum coverage. Use when the user wants the full treatment across all quality dimensions. For standard 2-3 specialist review, use review-ui.

  Triggers: "full UI review", "comprehensive UI review", "review everything about this UI", "thorough UI audit", "full quality audit", "review-ui-full", "complete UI review".
argument-hint: '[path | file | directory | screenshot | "diff" | "staged" | "pr" | "all"]'
allowed-tools: Bash, Read, Grep, Glob, TodoWrite, Agent
---

# Full UI Review

Coordinate a comprehensive 5-specialist + verifier UI quality review via the team lead.

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

The full review dispatches the `ui-review-lead` team lead logic, which internally dispatches all 5 specialists + verifier.

**Dispatch via `general-purpose`, not the plugin namespace** -- plugin-namespaced dispatch silently strips the `Agent` tool the lead needs for its 5 sub-specialists. Canonical rationale and failure behavior: the RUNTIME DISPATCH NOTE at the top of `agents/ui-review-lead.md`; do not restate it here or elsewhere. Read `${CLAUDE_PLUGIN_ROOT}/agents/ui-review-lead.md` and inline its full body as the prompt prefix. Omit `model` -- the team lead and every specialist it dispatches inherit the session model (always the strongest available Claude):

```
Agent({
  subagent_type: "general-purpose",
  prompt: "<full body of agents/ui-review-lead.md> + <scope, platform, project context, evidence availability>",
  description: "Full UI quality review (5 specialists + verifier)"
})
```

The team lead handles:
1. Dispatching visual + responsive + accessibility in parallel
2. Dispatching motion + runtime in parallel
3. Running the verifier on all findings
4. Merging into the unified report with per-dimension verdicts

### Execution mode

The team lead and its specialists inherit the session model; never dispatch to, or wait on, a specific named model. The 5-specialist parallel dispatch is the default for this workflow because it is what buys maximum coverage -- independent context per dimension, true parallelism, and a dedicated verification pass. Only if the Agent tool is unavailable in the current environment may the orchestrator fall back to running the review dimensions sequentially inline in the main context; this never weakens the read-only guarantee (Hard rule 1 in `agents/ui-review-lead.md`) or skips the verifier pass (Hard rule 6).

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

### Acceptance criteria (check before presenting)

| Check | Pass condition |
|---|---|
| Verdict table | 5 dimension rows, each with a verdict from that dimension's verdict set |
| Verifier ran | "Verification Notes" section present, listing removed/downgraded/merged findings (or explicitly "none") |
| Finding format | Every finding carries [SEVERITY] [CONFIDENCE] plus Surface, Issue, Why it matters, Evidence, Recommended change |
| Geometry rule | No spatial claim above "Possible issue" confidence without geometry evidence (`${CLAUDE_PLUGIN_ROOT}/references/09-evidence-pipeline.md`, "Geometry evidence rule") |
| Ordering | Findings numbered, CRITICAL first, TASTE last |

If the lead's report fails a check, re-dispatch once naming the concrete failure; on a second
failure, apply the verifier's rules (`${CLAUDE_PLUGIN_ROOT}/agents/ui-verifier.md`) to the raw
specialist findings directly before presenting -- never present an unverified report.

## Step 5: Write learnings

If the review surfaced non-obvious patterns, write to GoodMem Learnings.

## When to use this vs review-ui

| Use `review-ui` when | Use `review-ui-full` when |
|---|---|
| Quick quality check | Pre-launch quality gate |
| Specific concern (alignment, a11y) | Comprehensive audit |
| Small scope (1-5 files) | Large scope or full project |
| Standard review | Maximum coverage needed |
| Cost/time matters | Quality is the priority |
