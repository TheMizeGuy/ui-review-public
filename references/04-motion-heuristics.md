# Motion Review Heuristics

## What good motion looks like

| Principle | What to look for |
|---|---|
| Purposeful | Motion communicates state change, spatial relationship, or feedback -- not decoration |
| Fast enough | Micro-interactions: 150-300ms. Page transitions: 200-500ms. Longer often feels sluggish |
| Natural curves | Spring physics > cubic-bezier for interactive elements. Ease-out for enter, ease-in for exit |
| Interruptible | User input can cancel or reverse in-flight motion |
| Non-blocking | UI remains usable during transitions; content accessible before animation completes |
| Property choice | Prefer `transform`/`opacity`/`filter`/`clip-path` (compositor-safe) over layout-affecting properties |
| Reduced-motion aware | Decorative motion disabled or softened when user asks for less motion |
| Consistent | Similar interactions produce similar motion across the product |

## Motion anti-patterns

| Anti-pattern | Why it's a problem | Severity |
|---|---|---|
| Animations that exist only for decoration | Slower, distracting, no information communicated | MEDIUM-HIGH |
| Transitions > 500ms for micro-interactions | Feels sluggish, delays task completion | MEDIUM |
| Animating `width`/`height`/`top`/`left`/`margin` | Triggers layout recalculation, causes jank | HIGH on perf-sensitive paths |
| Parallax or scroll effects that fight the task | Distracts from content, can cause nausea | MEDIUM-HIGH |
| Motion that delays access to content | Content exists but user can't interact until animation finishes | HIGH |
| No `prefers-reduced-motion` fallback | Accessibility requirement, vestibular disorders | CRITICAL |
| Entrance animations on every page load | Repetitive, annoying on repeat visits | MEDIUM |
| Bouncy/elastic spring that overshoots content area | Feels unpolished, content flashes | MEDIUM |
| Animations that restart when scrolling back | Performative, not functional | LOW-MEDIUM |
| Loading spinners without timeout or fallback | Infinite spinners erode trust | HIGH |

## Spring physics reference

| Use case | Response | Damping | Duration equivalent |
|---|---|---|---|
| Micro-interaction (button press) | 0.6-0.8 | 0.6-0.8 | 100-200ms |
| Component enter/exit | 0.4-0.6 | 0.5-0.7 | 200-400ms |
| Page transition | 0.3-0.5 | 0.7-0.9 | 300-500ms |
| Drag release/snap | Variable | 0.5-0.7 | Context-dependent |

## CSS motion properties

### Compositor-safe (prefer these)

- `transform: translate()`, `scale()`, `rotate()`
- `opacity`
- `filter` (blur, brightness, etc.)
- `clip-path`

### Layout-triggering (avoid animating)

- `width`, `height`, `min-*`, `max-*`
- `top`, `right`, `bottom`, `left`
- `margin`, `padding`
- `border-width`
- `font-size`

### Modern CSS motion

| Feature | Status | Notes |
|---|---|---|
| View Transitions API | Production-ready (2025+) | SPA + MPA, GPU composited |
| Scroll-driven animations | Production-ready (Sept 2025) | `scroll()` for progress, `view()` for intersection |
| CSS `@starting-style` | Supported | Entry animations for display:none elements |
| CSS Anchor Positioning | Promising but new | Tooltip/popover positioning |

## Reduced motion implementation

The correct approach: motion as progressive enhancement.

```css
/* Default: no motion */
.card { opacity: 1; transform: none; }

/* Add motion for users who accept it */
@media (prefers-reduced-motion: no-preference) {
  .card {
    animation: fade-in 0.3s ease both;
    animation-timeline: view();
  }
}
```

### What "reduced motion" means for different animations

| Animation type | Reduce to |
|---|---|
| Decorative entrance | Remove entirely |
| Functional transition (page/state) | Instant crossfade or simple opacity |
| Loading/progress indicator | Keep, but simplify |
| Scroll-driven parallax | Remove |
| Micro-interaction feedback | Keep if < 100ms, remove if longer |

## Evidence requirements for motion findings

| Claim | Minimum evidence |
|---|---|
| "Animation is excessive/sluggish" | Video or trace + timing metadata |
| "Reduced motion is ignored" | Reduced-motion mode run or code/config evidence |
| "Animation causes jank" | Trace/video showing frame drops, or layout-triggering property identification |
| "Animation delays content access" | Trace timing showing content inaccessible during animation |
| "Spring feels wrong" | Compared against reference parameters + subjective assessment |

## Platform-specific motion expectations

### Web
- View Transitions API for page transitions
- `scroll-timeline` for scroll-driven effects
- `@starting-style` for element entry
- Spring physics via Motion library or CSS springs

### Apple (SwiftUI)
- `withAnimation(.spring)` for state changes
- `matchedGeometryEffect` for shared element transitions
- `PhaseAnimator` / `KeyframeAnimator` for complex sequences
- `.sensoryFeedback` for haptic confirmation
- `symbolEffect` for SF Symbol animations

### Android (Compose)
- `animateContentSize()` for layout changes
- `AnimatedVisibility` for enter/exit
- `spring()` spec for physics-based motion
- `Animatable` for custom animations
- Shared element transitions via navigation
