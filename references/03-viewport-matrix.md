# Viewport Matrix and Responsive Review

## Default web viewport families

| Family | Widths (px) | Why include |
|---|---|---|
| Narrow mobile | 320, 360 | Worst-case wrapping, cramped controls |
| Modern mobile | 390, 430 | Common iPhone/Android widths; production issues |
| Small tablet / large phone landscape | 568, 667, 768 | Nav changes, stacked layout stress |
| Tablet / compact desktop | 820, 1024 | Breakpoint mistakes, sidebar collapse, split view |
| Laptop | 1280, 1440 | Baseline desktop behavior |
| Wide desktop | 1728+ | Excessive line length, dead space, max-width issues |

## Additional review modes

- Dark and light theme (if both exist)
- Portrait and landscape (where layout changes)
- Reduced-motion mode (for motion-heavy interfaces)
- High-zoom / large-text mode (where product must support it)
- Keyboard-open scenario (for form-heavy mobile views)

## Android device matrix (from first-party guidance)

| Device class | Size | Window size class |
|---|---|---|
| Phone compact portrait | ~360dp | Compact |
| Phone compact landscape | ~640dp | Medium |
| Foldable closed | ~360dp | Compact |
| Foldable open/flat | ~580dp | Medium/Expanded |
| 8-inch tablet | ~600dp | Medium |
| 10.5-inch tablet | ~720dp | Expanded |
| 13-inch Chromebook | ~840dp+ | Expanded/Large |

## Apple device matrix

| Device class | Viewport | Notes |
|---|---|---|
| iPhone SE | 375pt | Smallest current |
| iPhone 16 | 393pt | Standard |
| iPhone 16 Pro Max | 430pt | Large |
| iPad Mini | 744pt | Compact iPad |
| iPad Air/Pro 11" | 820pt | Standard iPad |
| iPad Pro 13" | 1024pt | Largest iPad |

## High-confidence viewport failures (always flag)

- Horizontal scrolling in primary content
- Clipped or truncated critical text
- Overlapping controls or text
- Controls hidden under browser chrome, sticky headers, or bottom bars
- Off-screen dialogs, sheets, or dropdowns
- Touch targets collapsing below usable size
- Broken focus visibility because overlays obscure the target
- Impossible or awkward navigation from layout collapse

## Quality defects (viewport-related)

- Excessive dead space on large displays
- Line lengths > 80ch on wide screens
- Component proportions comically stretched or cramped
- Awkward breakpoint jumps that feel unstable
- Important actions in hard-to-reach areas without reason
- Poor container behavior inside sidebars, cards, drawers, panels

## Container-aware review rules

Record three geometry contexts for any flagged issue:

1. **Viewport size** -- the browser/device viewport
2. **Container width** -- the parent container of the affected component
3. **Component box** -- the component's own rendered dimensions

Without all three, a reviewer cannot tell if a component is broken, intentionally constrained, or shown in the wrong parent context.

## Mobile and safe-area heuristics

| Heuristic | Why |
|---|---|
| Respect `env(safe-area-inset-*)` | Prevents bottom nav and top chrome collisions on notched devices |
| Use modern viewport units (`svh`, `dvh`) over `100vh` | Avoids clipped full-height layouts under dynamic browser chrome |
| Keep primary actions reachable (thumb zone) | Prevents thumb-zone and keyboard occlusion issues |
| Avoid hover-only interactions | Touch users need an equivalent path |
| Test keyboard-open for forms | Inputs near top/bottom become unusable otherwise |

## De-duplication strategy

Do not report the same defect at six widths. Group by failure family:

```
Overflow family: cart summary clips action buttons from 390px down through 320px.
Worst observed at 320px portrait.
```

## Severity rules for viewport findings

| Condition | Severity |
|---|---|
| Core task blocked on common device width | CRITICAL or HIGH |
| Important content clipped on one common viewport family | HIGH |
| Layout degraded but usable on common viewport family | MEDIUM |
| Cosmetic issue only on fringe width or rare orientation | LOW |
| Pure style preference with no usability impact | TASTE |
