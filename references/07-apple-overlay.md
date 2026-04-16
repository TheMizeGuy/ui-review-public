# Apple Platform Overlay

Additional review expectations when reviewing Apple platform UIs (SwiftUI, UIKit, iOS, iPadOS, macOS, visionOS).

## Design philosophy

| Principle | Meaning | What to check |
|---|---|---|
| Clarity | Text legible at every size; icons precise; focus on functionality | SF Pro/Compact, SF Symbols, generous whitespace, readable contrast |
| Deference | Interface never competes with content | Content fills screen, minimal chrome, translucent materials |
| Depth | Visual layers convey hierarchy and vitality | Translucent backgrounds, spring animations, layered sheets |

## Liquid Glass (iOS 26+)

| Check | Expectation |
|---|---|
| System bars use glass materials | TabBar, NavigationBar, ToolBar adopt Liquid Glass |
| Custom bars integrate with system glass | Match system material, don't fight it |
| Content readable behind glass | Sufficient contrast and vibrancy |
| Not overused on every surface | Glass is for system chrome and prominent surfaces, not everything |

## Typography

| Check | Expectation | Anti-pattern |
|---|---|---|
| System fonts (SF Pro, SF Compact, SF Mono) | Used for text unless brand requires custom | Non-system font without Dynamic Type support |
| Dynamic Type support | All text scales with system setting | Fixed font sizes |
| `@ScaledMetric` for spacing/sizing | Spacing scales proportionally with text | Fixed padding that doesn't scale |
| Accessibility sizes handled | Layout adapts at largest accessibility sizes | Content clips or overlaps at AX5 |
| `ViewThatFits` or `AnyLayout` | Graceful layout switching at large text | Content truncated instead of reflowed |

## Colors

| Check | Expectation |
|---|---|
| Semantic system colors | `Color.primary`, `.secondary`, `.accent` etc. |
| Asset catalog colors with dark mode variants | Named colors with light/dark/high-contrast |
| No hard-coded UIColor/Color literals in views | Use semantic tokens |
| Sufficient contrast in both themes | Test light AND dark mode |
| High contrast mode support | `@Environment(\.colorSchemeContrast)` |

## SF Symbols

| Check | Expectation |
|---|---|
| SF Symbols used for system concepts | Consistent with platform vocabulary |
| Appropriate rendering mode | Monochrome, hierarchical, palette, or multicolor |
| Symbol effects for state changes | `.symbolEffect(.bounce)`, `.pulse`, `.variableColor` |
| Proper sizing with text | `.imageScale(.medium)` or matching text style |

## Navigation

| Check | Expectation | Anti-pattern |
|---|---|---|
| NavigationStack/NavigationSplitView | System navigation with proper back behavior | Custom navigation replacing system patterns |
| TabView for top-level destinations | Standard tab bar | More than 5 tabs without More |
| Sheets for modal content | `.sheet()`, `.fullScreenCover()` | Custom modal overlays |
| Toolbar items for contextual actions | `.toolbar { }` | Floating action buttons (non-Apple pattern) |
| iPad adaptive layout | NavigationSplitView with two/three columns | Phone layout stretched to iPad |

## Touch targets

| Standard | Minimum |
|---|---|
| Apple HIG | 44x44pt |
| Small controls in toolbars | Still 44x44pt tap area even if visually smaller |

## Haptics

| Interaction | Expected haptic |
|---|---|
| Toggle/switch | `.impact(.light)` or `.selection` |
| Delete/destructive | `.notification(.warning)` |
| Success/completion | `.notification(.success)` |
| Error | `.notification(.error)` |
| Selection change | `.selection` |
| Drag threshold | `.impact(.medium)` at snap points |

## Accessibility (Apple-specific)

| Check | SwiftUI implementation |
|---|---|
| VoiceOver labels | `.accessibilityLabel()` on all interactive elements |
| VoiceOver hints | `.accessibilityHint()` for non-obvious actions |
| Traits | `.accessibilityAddTraits(.isButton)` where needed |
| Grouping | `.accessibilityElement(children: .combine)` |
| Custom actions | `.accessibilityAction()` for swipe actions |
| Rotor support | Custom rotor items for complex content |
| Large Content Viewer | `.accessibilityShowsLargeContentViewer()` for toolbar items |
| Reduce Motion | Check `@Environment(\.accessibilityReduceMotion)` |
| Reduce Transparency | Check `@Environment(\.accessibilityReduceTransparency)` |

## Motion (Apple-specific)

| Check | Expectation |
|---|---|
| Spring animations | `withAnimation(.spring)` over `.easeInOut` |
| `matchedGeometryEffect` for shared elements | Smooth hero transitions |
| Phase/Keyframe animators for complex sequences | Not nested `withAnimation` blocks |
| Gesture-driven interruptible transitions | `DragGesture` + spring |
| State-change animations | `withAnimation` wrapping state changes, not view modifiers |

## iPad and adaptive layout

| Check | Expectation |
|---|---|
| NavigationSplitView | Two or three-column layout on iPad |
| Keyboard shortcuts | `.keyboardShortcut()` for common actions |
| Context menus | `.contextMenu()` on long-press targets |
| Drag and drop | Support where content can be shared |
| Stage Manager | App works in arbitrary window sizes |
| Pointer/hover | `.hoverEffect()` on interactive elements |

## Severity guidance (Apple overlay)

| Condition | Severity |
|---|---|
| No Dynamic Type support | HIGH |
| Custom navigation replacing system patterns | MEDIUM-HIGH |
| Hard-coded colors, no dark mode | HIGH |
| Touch targets below 44pt | HIGH |
| No VoiceOver labels on interactive controls | CRITICAL |
| No reduced motion support | CRITICAL |
| Phone layout on iPad with no adaptation | MEDIUM |
| Missing haptic feedback on significant actions | LOW-MEDIUM |
