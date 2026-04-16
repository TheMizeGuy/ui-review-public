# Accessibility Review Checklist

## Universal requirements (all platforms)

### Semantics and labeling

| Check | Standard | Severity if failed |
|---|---|---|
| All interactive elements have accessible names | WCAG 4.1.2 | CRITICAL |
| Form inputs have associated labels | WCAG 1.3.1 | CRITICAL |
| Images have alt text (decorative = `alt=""`) | WCAG 1.1.1 | CRITICAL (functional) / LOW (decorative) |
| Landmarks/regions used for page structure | WCAG 1.3.1 | MEDIUM |
| Headings follow logical hierarchy | WCAG 1.3.1 | MEDIUM |
| Data tables have headers | WCAG 1.3.1 | HIGH |
| Language declared | WCAG 3.1.1 | MEDIUM |
| Status changes announced to assistive tech | WCAG 4.1.3 | HIGH |

### Keyboard and focus

| Check | Standard | Severity if failed |
|---|---|---|
| All functionality reachable by keyboard | WCAG 2.1.1 | CRITICAL |
| No keyboard traps | WCAG 2.1.2 | CRITICAL |
| Focus order matches visual order | WCAG 2.4.3 | HIGH |
| Focus visible on all interactive elements | WCAG 2.4.7 | CRITICAL |
| Focus not obscured by sticky/fixed elements | WCAG 2.4.11 | HIGH |
| Skip navigation link available | WCAG 2.4.1 | MEDIUM |
| Custom widgets support expected keyboard patterns | WCAG 2.1.1 | HIGH |
| Escape closes modals/overlays | WCAG 2.1.1 | HIGH |
| Focus returns to trigger after overlay closes | Best practice | MEDIUM |

### Visual accessibility

| Check | Standard | Severity if failed |
|---|---|---|
| Text contrast >= 4.5:1 (normal) / 3:1 (large) | WCAG 1.4.3 | CRITICAL |
| Non-text contrast >= 3:1 | WCAG 1.4.11 | HIGH |
| Not relying on color alone for meaning | WCAG 1.4.1 | HIGH |
| Text resizable to 200% without loss | WCAG 1.4.4 | HIGH |
| Content reflows at 320px (no horizontal scroll) | WCAG 1.4.10 | HIGH |
| Spacing adjustable without loss of content | WCAG 1.4.12 | MEDIUM |

### Motor accessibility

| Check | Standard | Severity if failed |
|---|---|---|
| Touch targets >= platform minimum (44pt Apple, 48dp Android, 24px WCAG 2.2) | WCAG 2.5.8 | HIGH-CRITICAL |
| No drag-only actions without alternative | WCAG 2.5.7 | HIGH |
| No multi-pointer required gestures without alternative | WCAG 2.5.1 | HIGH |
| Target spacing sufficient (no accidental activation) | WCAG 2.5.8 | MEDIUM |

### Motion accessibility

| Check | Standard | Severity if failed |
|---|---|---|
| `prefers-reduced-motion` respected | WCAG 2.3.3 | CRITICAL |
| No content flashes > 3/second | WCAG 2.3.1 | CRITICAL |
| Auto-playing content can be paused/stopped | WCAG 2.2.2 | HIGH |
| Motion not required for any functionality | WCAG 2.5.4 | HIGH |

### Dynamic content

| Check | Failure mode | Severity |
|---|---|---|
| Live regions announce updates | `aria-live="polite"` for non-urgent, `"assertive"` for critical | HIGH |
| Loading states communicated | `role="status"` or `role="alert"` | HIGH |
| Error messages associated with inputs | `aria-describedby` + `aria-invalid` | HIGH |
| Toast/snackbar messages accessible | Announced via live region with sufficient display time | MEDIUM |

## APCA contrast targets (WCAG 3.0 preview)

| APCA Score (Lc) | Use |
|---|---|
| 90+ | Body text (small, regular weight) |
| 75+ | Large text, bold body |
| 60+ | Headlines, large UI text |
| 45+ | Large non-text elements, icons |
| 30+ | Decorative, non-essential |
| < 15 | Invisible; never for functional elements |

## Screen reader patterns

| Pattern | Implementation |
|---|---|
| Live regions | `aria-live="polite"` non-urgent; `"assertive"` critical |
| Loading states | `role="status"` or `role="alert"` with descriptive text |
| Skip nav | `<a href="#main" class="skip-link">Skip to main content</a>` first focusable |
| Landmarks | `<header>`, `<nav>`, `<main>`, `<footer>`, `<aside>` |
| Form errors | `aria-describedby` linking input to error; `aria-invalid="true"` |
| Disclosure | `aria-expanded`, `aria-controls` on trigger |
| Tabs | `role="tablist"`, `role="tab"`, `role="tabpanel"`, arrow key navigation |
| Dialogs | `role="dialog"`, `aria-modal="true"`, focus trap, close on Escape |

## Focus ring reference

```css
:focus-visible {
  outline: 3px solid oklch(55% 0.25 260);
  outline-offset: 2px;
  border-radius: 4px;
}

:focus:not(:focus-visible) {
  outline: none;  /* mouse/touch don't see the ring */
}
```

## Touch target reference

| Standard | Minimum | Recommended |
|---|---|---|
| Apple HIG | 44x44pt | 44x44pt |
| Material Design 3 | 48x48dp | 48x48dp |
| WCAG 2.2 (2.5.8) | 24x24 CSS px | 44x44 CSS px |

## Additional media queries to check

```css
@media (prefers-contrast: more)    { /* increase contrast */ }
@media (prefers-contrast: less)    { /* reduce harsh contrasts */ }
@media (forced-colors: active)     { /* Windows High Contrast */ }
@media (prefers-reduced-transparency: reduce) { /* solid backgrounds */ }
```

## Confidence rules for accessibility findings

- Automation finds violation + artifact supports it: **strong finding**
- Automation clean but screenshots/trace suggest issue: **"needs manual verification"**
- Input lacks semantics or accessibility-tree evidence: **soften claims, don't guess**
- Never say "accessible" based solely on clean automation scan
