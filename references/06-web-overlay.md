# Web Platform Overlay

Additional review expectations when reviewing web UIs (HTML/CSS/JS frameworks).

## Layout fundamentals

| Check | Expectation | Anti-pattern |
|---|---|---|
| Grid vs Flexbox | Grid for 2D page/section layout; Flexbox for 1D alignment | Flexbox for card grids, Grid for single-row nav |
| Subgrid | Use when nested elements need shared tracks (card grids, forms) | Nested Grid without subgrid when tracks should align |
| Container queries | Components respond to container size, not just viewport | Viewport media queries for component-level layout |
| Logical properties | `padding-inline`, `margin-block`, `max-inline-size` for i18n | Physical properties (`padding-left`) when layout should flip for RTL |
| Modern viewport units | `svh`, `dvh` over legacy `100vh` | `100vh` causing content behind mobile browser chrome |
| Intrinsic sizing | Let content determine size where appropriate | `width: 100%` everywhere instead of `fit-content`/`min-content` |
| Fluid spacing | `clamp()`, `min()`, `max()` for responsive without breakpoints | Fixed `px` spacing at all viewport sizes |

## Typography (web-specific)

| Check | Expectation |
|---|---|
| Body text >= 16px on mobile | Prevents iOS auto-zoom on inputs |
| Line length 45-75 characters | Readability standard |
| Variable fonts over multiple statics | One file replaces 6-12 |
| Subset to Latin when possible | Reduces font payload |
| `font-display: swap` or `optional` | Controls FOIT/FOUT |
| System font fallback stack | Prevents blank text on font failure |
| Tabular figures for data columns | Prevents jumping numbers |

## Color (web-specific)

| Check | Expectation |
|---|---|
| OKLCH for core tokens | Perceptually uniform, P3 gamut |
| Three-tier token system | Primitive -> semantic -> component |
| `light-dark()` for theme | CSS-native dark mode |
| `color-mix()` for variants | Derived shades from base tokens |
| No hard-coded hex in components | Use token references |

## CSS architecture

| Check | Expectation |
|---|---|
| `@layer` for cascade control | Predictable specificity |
| Design tokens in CSS custom properties | Or DTCG format compiled to CSS vars |
| `content-visibility: auto` for long pages | 7x rendering improvement on heavy pages |
| No `!important` outside reset/utility | Cascade smell |
| `@property` only when needed | Typed/animatable custom properties |

## Responsive checks (web-specific additions)

| Check | Why |
|---|---|
| Container queries for reusable components | Components should adapt to their container, not the viewport |
| No `float` for layout | Legacy, causes unexpected wrapping |
| No `user-scalable=no` | Accessibility violation, prevents zoom |
| Form inputs don't trigger zoom on iOS | Font size >= 16px on inputs |
| Sticky elements don't obscure focused content | WCAG 2.4.11 |

## Performance-aware design

| Signal | Threshold | Why the reviewer cares |
|---|---|---|
| LCP | < 2.5s | Slow first impression |
| INP | < 200ms | Interactions feel immediate |
| CLS | < 0.1 | Layout instability users experience as jank |

### Design choices worth flagging

- Autoplay hero video on primary path
- Heavy glass/blur/shadow on dense screens
- Large images without optimization (format, sizing, loading)
- Content that shifts during load
- Runtime-loaded styles that restack the interface
- Unvirtualized huge lists or tables
- Long transitions that block interaction feedback

## Hover/focus parity

Web UIs must work for both mouse and keyboard users:

| Mouse state | Keyboard equivalent |
|---|---|
| `:hover` | `:focus-visible` |
| Click | Enter/Space |
| Right-click | Context menu key |
| Drag | Arrow keys or accessible alternative |

## Modern CSS features to check for

| Feature | Status | Notes |
|---|---|---|
| Container queries | Baseline 2023 | Use for component responsiveness |
| `@layer` | Baseline 2022 | Use for cascade control |
| `oklch()` / `color-mix()` | Baseline 2023 | Use for color tokens |
| `:has()` | Baseline 2023 | Enables parent selection |
| View Transitions | Baseline late 2025 | SPA + MPA transitions |
| Scroll-driven animations | Baseline Sept 2025 | No-JS scroll effects |
| `@starting-style` | Baseline 2024 | Entry animations |
| Popover API | Baseline 2024 | No-JS popovers |
| `content-visibility` | Baseline Sept 2025 | Rendering performance |

## Frameworks to consider

| Category | Current leaders |
|---|---|
| CSS framework | Tailwind CSS v4 (OKLCH, CSS-only config) |
| Component library | shadcn/ui (Radix + Tailwind, full ownership) |
| Headless primitives | React Aria, Base UI |
| Design tokens | Style Dictionary, Tokens Studio, Terrazzo |
| Animation | Motion (WAAPI-backed), CSS native |
| Meta-frameworks | Next.js, Nuxt, SvelteKit, Astro |
