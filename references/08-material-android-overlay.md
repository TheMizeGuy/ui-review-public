# Material / Android Platform Overlay

Additional review expectations when reviewing Android platform UIs (Jetpack Compose, Material 3, XML views).

## Adaptive quality (core Android expectation)

Android is a multi-form-factor platform: phones, tablets, foldables, desktops (Chromebook), cars, TVs, XR. The plugin evaluates adaptive quality first, Material aesthetics second.

### Window size classes

| Class | Width breakpoint | Typical devices |
|---|---|---|
| Compact | < 600dp | Phones portrait |
| Medium | 600-839dp | Foldable open, small tablet, phone landscape |
| Expanded | 840-1199dp | Tablets, Chromebooks |
| Large | 1200-1599dp | Desktop windows |
| Extra-large | 1600dp+ | Large monitors |

### Adaptive layout checks

| Check | Expectation | Anti-pattern |
|---|---|---|
| Width-class-aware layout | Different layouts for compact vs expanded | Single phone layout at all sizes |
| Canonical layouts | List-detail, supporting pane, feed patterns at appropriate sizes | Ignoring canonical layout guidance |
| Fold/unfold behavior | Core tasks survive fold state changes | Broken layout when foldable opens/closes |
| Multi-window support | App works in split-screen and free-form windows | Crash or broken layout in multi-window |
| Desktop-class windows | Not stranded in phone mode on Chromebook-sized widths | Phone UI with massive empty space |

### Navigation checks

| Check | Expectation | Anti-pattern |
|---|---|---|
| Bottom nav for compact | Standard bottom navigation bar | Rail on phone |
| Navigation rail for expanded | Side rail with labels on tablets/desktop | Bottom nav still on wide screens |
| Drawer for extra-large | Permanent/modal drawer with full labels | Rail persisting at desktop widths |
| Top-level destinations reachable | Primary nav always accessible | Nav hidden behind hamburger on tablet |
| Navigation adapts to width changes | Smooth transition between nav types on resize | Nav breaks on configuration change |

## Touch targets

| Standard | Minimum |
|---|---|
| Material Design 3 | 48x48dp (touch target area, not visual size) |
| Spacing between targets | Sufficient to prevent accidental activation |

## Compose accessibility

| Check | Implementation | Anti-pattern |
|---|---|---|
| Standard components preserve semantics | Material components have built-in semantics | Replacing Material with custom without `semantics {}` |
| Custom components expose semantics | `Modifier.semantics { }` block | Custom composables with no accessibility |
| Content descriptions | `contentDescription` on icons and images | Icon buttons without description |
| Traversal order | `Modifier.semantics { traversalIndex = N }` when needed | Illogical TalkBack reading order |
| State descriptions | `stateDescription` for toggles and selections | Switch without on/off announcement |
| Actions | `customActions` for complex widgets | Hidden functionality |
| Testing | Compose accessibility checks (ATF) from 1.8.0+ | No automated accessibility testing |

## Material 3 component checks

| Component | What to check |
|---|---|
| Top app bar | Proper scroll behavior (collapse, pin, exit until) |
| FAB | Correct placement, appropriate for primary action, accessible |
| Bottom sheet | Handles gestures, keyboard, back navigation |
| Snackbar | Accessible timeout, action button, dismissible |
| Navigation | Adapts to window size class |
| Cards | Proper elevation, clickable area includes full card |
| Dialogs | Focus trap, back dismisses, centered on screen |
| Text fields | Label animation, error state, helper text, character count |

## Motion (Android-specific)

| Check | Expectation |
|---|---|
| Shared element transitions | Via navigation for connected experiences |
| `animateContentSize()` | For layout size changes |
| `AnimatedVisibility` | For enter/exit with proper specs |
| Spring specs | `spring()` with appropriate stiffness/damping |
| Deterministic test support | Animations testable with `ComposeTestRule` clock control |
| Jank detection | No skipped frames on common transitions |

## Configuration change handling

| Event | Expectation |
|---|---|
| Rotation | Layout adapts, state preserved |
| Fold/unfold | Layout adapts to new size class |
| Split-screen enter/exit | Layout adapts, no crash |
| Locale change | Layout handles RTL/LTR, text scales |
| Font scale change | Layout handles Dynamic Type equivalent |
| Dark mode toggle | Colors update, no restart required |

## Testing expectations

| Test type | Official surface |
|---|---|
| Screenshot tests | Compose screenshot testing, Compose Preview tests |
| Accessibility checks | Compose ATF checks (1.8.0+), Espresso accessibility |
| Configuration changes | Espresso Device API (rotation, fold, multi-window) |
| Animation tests | `ComposeTestRule` with clock control + `captureToImage()` |
| Jank detection | Android Studio jank detection, trace-based diagnosis |

## Severity guidance (Android overlay)

| Condition | Severity |
|---|---|
| Core path breaks on fold, tablet, or multi-window | CRITICAL-HIGH |
| Phone-only layout on tablet with no blocking issue | MEDIUM |
| Touch target below 48dp on important controls | HIGH |
| Material semantics replaced without proper custom semantics | HIGH |
| Navigation ignores width-class ergonomics | MEDIUM-HIGH |
| Minor visual deviation from Material with no usability cost | LOW-TASTE |
| No accessibility semantics on custom components | CRITICAL |
| Configuration change causes state loss or crash | CRITICAL |

## Design rule

Evaluate adaptive quality and input ergonomics first. Material-style coherence second. Don't over-penalize apps that are intentionally branded or visually distinctive but still behave correctly on Android.
