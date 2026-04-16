# Typography, Color, and Layout Foundations

Quick-reference for review agents on design fundamentals.

## Typography

### Type scale best practices

| Metric | Guideline |
|---|---|
| Minimum body | 16px on mobile (prevents iOS auto-zoom) |
| Line length | 45-75 characters desktop, shorter mobile |
| Line height | 1.5 for body, 1.2-1.3 for headings |
| Paragraph spacing | >= line height |
| Font families loaded | Max 2 (1 preferred for performance) |
| Heading hierarchy | Clear visual distinction between h1-h6 used |

### Variable fonts

- One file replaces 6-12 static files
- Custom axes (weight, width, optical size) for responsive typography
- 95%+ browser support
- Enable optical sizing for better small/large text rendering

### Fluid typography

```css
/* Scale from 16px at 320px to 20px at 1440px */
font-size: clamp(1rem, 0.857rem + 0.714vw, 1.25rem);
```

Utopia and Fluid Type Scale Calculator generate these scales.

### Font loading

| Strategy | Use when |
|---|---|
| `font-display: swap` | Default; shows fallback then swaps |
| `font-display: optional` | Performance-critical; may not show custom font |
| Preload critical fonts | `<link rel="preload" as="font">` for above-fold text |
| Subset to Latin | Reduces payload when only Latin chars needed |

### Typography anti-patterns

- More than 2 font families
- Fixed `px` sizes without fluid/rem
- Full font files when only Latin needed
- No fallback stack
- Ignoring optical sizing on variable fonts
- Line lengths > 80ch
- Body text < 12px
- Gray-on-gray with insufficient contrast
- All caps body text
- Decorative fonts for body content

## Color

### OKLCH (recommended standard)

Perceptually uniform color space. 92%+ browser support. Tailwind v4 default.

```css
/* Three-tier token system */
:root {
  /* Primitive */
  --blue-500: oklch(55% 0.25 260);
  /* Semantic */
  --color-primary: var(--blue-500);
  /* Component */
  --button-bg: var(--color-primary);
}
```

### Dark mode engineering

```css
/* Modern approach */
:root {
  color-scheme: light dark;
  --surface: light-dark(oklch(98% 0 0), oklch(15% 0 0));
  --text: light-dark(oklch(15% 0 0), oklch(90% 0 0));
}
```

Dark mode is not just inverted colors. Adjustments needed:
- Reduce chroma/saturation on vibrant colors
- Lighten surfaces slightly (not pure black)
- Adjust elevation shadows (lighter in dark mode)
- Test contrast in both modes independently

### Contrast standards

| Standard | Body text | Large text | Non-text |
|---|---|---|---|
| WCAG 2.2 AA | 4.5:1 | 3:1 (18px+ bold or 24px+) | 3:1 |
| WCAG 2.2 AAA | 7:1 | 4.5:1 | -- |
| APCA (Lc) | 90+ | 60+ | 45+ |

### Color anti-patterns

- Too many accent colors (>3)
- Decorative-only saturation with no hierarchy purpose
- Color as sole status indicator (needs icon/text/pattern)
- Hard-coded hex in components instead of tokens
- Inconsistent opacity values across similar elements
- Pure white backgrounds in dark mode (glare)
- Pure black backgrounds (#000) that feel harsh

## Layout

### Grid fundamentals

| Use | When |
|---|---|
| CSS Grid | 2D page/section layout, any time rows AND columns matter |
| Flexbox | 1D alignment within a container (nav items, button groups) |
| Subgrid | Nested elements that need shared tracks (card grids, form labels) |

### Spacing system

| Approach | Implementation |
|---|---|
| Base-4 or base-8 scale | 4, 8, 12, 16, 24, 32, 48, 64, 96... |
| Fluid spacing | `clamp(1rem, 0.5rem + 2vw, 3rem)` |
| Logical properties | `padding-inline`, `margin-block` for i18n |
| Consistent vertical rhythm | Multiples of base unit throughout |

### Layout anti-patterns

- Flexbox for 2D card grids (use Grid)
- Nested Grid without subgrid when alignment is needed
- `float` for layout (text-wrap-around is the only valid use)
- `width: 100%` everywhere instead of intrinsic sizing
- Fixed pixel breakpoints when `clamp()` works
- Disabling zoom (`user-scalable=no`)
- `100vh` on mobile (use `100svh` or `100dvh`)
- No `max-width` on text content (unbounded line length)

### Alignment validation

When reviewing alignment, check for:

| Check | What to look for |
|---|---|
| Invisible grid lines | Elements should sit on consistent grid tracks |
| Baseline alignment | Text across columns should share baselines |
| Edge alignment | Elements in the same group should share leading/trailing edges |
| Optical alignment | Some elements (icons, rounded shapes) need optical correction |
| Consistent gutters | Gap between columns/rows should be uniform |
| Intentional breaking | If alignment breaks, it should be obviously intentional |

### Whitespace hierarchy

| Principle | Guidance |
|---|---|
| Groups closer together | Related items have smaller gaps |
| Sections further apart | Unrelated sections have larger gaps |
| Scale matches importance | More important separations get more space |
| Breathing room | Dense UI needs intentional empty space |
| Edge padding | Content shouldn't touch container edges |
