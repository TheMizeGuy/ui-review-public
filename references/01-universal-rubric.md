# Universal Review Rubric

## Layer 1: Dimensions (apply to every UI)

| Dimension | What to judge | Defect signals |
|---|---|---|
| Layout and alignment | Grid logic, invisible axes, consistent margins, element alignment, balance, visual weight distribution | Off-axis elements, ragged edges, unintentional drift, inconsistent gutters, broken grid relationships |
| Spacing and grouping | Proximity, separation, rhythm, whitespace hierarchy, breathing room | Groups collapsing, arbitrary gaps, padding drift, dense/noisy clusters, uneven vertical rhythm |
| Typography and readability | Type scale, hierarchy, line length, line height, truncation, emphasis, font loading | Body text < 16px mobile, weak hierarchy, lines > 75ch, clipped labels, color-only emphasis, FOIT/FOUT |
| Color and visual hierarchy | Contrast, semantic color use, interactive emphasis, theme consistency, CTA priority | Low-contrast text, too many accents, decorative-only saturation, unclear CTA priority, broken dark mode |
| Component coherence | Reuse, consistency, system fit, token discipline, variant logic | Same component styled 3 ways, library defaults unmodified, inconsistent radii/shadows/borders |
| State completeness | Empty, loading, error, success, disabled, validation, extreme content | Only happy-path exists; blank/broken fallback states; no skeleton/spinner; missing validation feedback |
| Content quality | Clarity, microcopy, placeholder leakage, scannability, action language | Lorem ipsum, generic marketing filler in product UI, labels that don't explain actions, truncation hiding meaning |
| Affordance and feedback | Can users tell what's interactive? What happened after acting? | Clickable text with no affordance, weak focus/pressed states, silent failures, ambiguous cursors |
| Accessibility fundamentals | Semantics, keyboard/focus, target size, labels, announcements, reduced motion | WCAG failures, hidden focus, tiny targets, unlabeled controls, motion with no opt-out |

## Layer 2: Platform overlays (apply when detected)

| Overlay | Applies when | Extra expectations |
|---|---|---|
| Web | DOM/CSS/browser UI detected | Responsive layout, hover/focus parity, reflow, non-text contrast, CLS/INP-aware design, container queries |
| Apple | SwiftUI/UIKit or Apple-style apps | System nav patterns, Dynamic Type, semantic colors, SF Symbols, 44pt targets, Liquid Glass, platform feel |
| Material/Android | Compose/Material or Android-style apps | 48dp targets, adaptive layouts, window size classes, Material component/state behavior, canonical layouts |
| Design file/mockup | Static Figma/export-only review | Visual + IA critique valid; runtime/a11y claims softened unless corroborated |

## Layer 3: Confidence classes

| Class | Meaning | When to use |
|---|---|---|
| Hard defect | Objective, should be fixed | Clipped text, absent focus ring, target below standard, WCAG AA failure, broken state |
| Quality defect | Strongly justified, alternatives exist | Weak hierarchy, noisy spacing, unclear action priority, inconsistent component treatments |
| Pattern smell | Commonly correlated with poor output | Unmodified library defaults, generic gradient hero, template card grid, AI-generated fingerprints |
| Taste note | Advisory only | Different visual direction might fit better; palette feels safe; more distinctive treatment possible |

## Severity scale

| Tag | Use for | Examples |
|---|---|---|
| CRITICAL | Blocks use, accessibility, or basic trust | Hidden focus, unusable viewport, missing labels on core controls, impossible-to-read text, key path broken |
| HIGH | Serious quality regression or likely user friction | Overlapping controls, broken reflow, confusing hierarchy, animation impedes use, color-only status |
| MEDIUM | Noticeable quality gap | Generic visual system, weak emphasis, awkward spacing, missing secondary states, stiff transitions |
| LOW | Minor polish issue | Slightly heavy shadow, inconsistent icon weight, minor rhythm drift, subtle alignment offset |
| TASTE | Non-blocking stylistic suggestion | Alternate font pairing, stronger visual personality, more distinctive treatment |

## Finding format

Every finding answers five questions: what, where, why, confidence, fix.

```
[SEVERITY] [CONFIDENCE] Dimension -- short title
Surface: <screen / component / viewport / state>
Issue: <concrete problem>
Why it matters: <user consequence>
Evidence: <artifact types or metrics that support this>
Recommended change: <specific fix direction with code if applicable>
```

## Objective vs subjective boundaries

### Flag strongly (objective enough)

- Clipped or truncated essential text
- Controls obscured by sticky/fixed elements
- Insufficient focus visibility
- Target sizes below platform/WCAG minimums
- Contrast failures for text or functional non-text
- Missing/broken empty/error/loading states where flow requires them
- Inconsistent/misleading action labels
- Color as sole status indicator
- Inaccessible icon-only controls
- Gesture-only critical actions with no visible alternative
- Horizontal scroll in primary content on common viewports
- Broken keyboard navigation paths

### Usually quality defects, not hard blockers

- Uneven spacing rhythm
- Hierarchy that doesn't make primary action obvious
- Overly dense or sparse layout
- Inconsistent component treatments across screens
- Unreadable data tables or chart legends
- Decorative motion that distracts from task
- Product UI with vague promotional copy instead of clear action language

### Usually taste-only

- Design feels generic
- Visual language lacks personality
- More distinctive typography could help
- Palette feels safe or over-familiar

These can still be useful findings, but they must not crowd out real defects.

## Key rule

The plugin is opinionated about quality, not dogmatic about style. A distinctive interface that breaks conventions for a reason scores better than a generic but clean template. A technically broken but visually trendy interface scores poorly.
