# Evidence Pipeline

## Review modes

| Mode | When to use | Required evidence | Confidence |
|---|---|---|---|
| Screenshot-only | User provides screenshots, no code access | Screenshots + OCR + user-supplied context | Lower -- findings stay soft on geometry claims |
| Code review | Source code available, no running app | Source files + static analysis | Medium -- can verify structure, not runtime |
| Browser-assisted | Web app with Playwright available | Screenshots + DOM + traces + a11y scans | High -- full evidence pipeline |
| Full evidence | Running app with all tooling | Everything above + video + metrics + computed styles | Highest -- verifier has maximum signal |

## Artifact bundle schema

Every review run should normalize artifacts into one directory:

```
review-run/
  manifest.json           # Review metadata, scope, mode
  screens/
    home/
      desktop-default.png
      mobile-default.png
      mobile-error.png
      header.element.png
  traces/
    home.trace.zip
  video/
    checkout-mobile.webm
  a11y/
    home.axe.json
  dom/
    home.desktop.dom.json
  metrics/
    home.desktop.layout.json
```

## Manifest fields

| Field | Purpose |
|---|---|
| `surface_type` | web, ios, android, desktop, design-file, screenshot |
| `screen_id` | Unique identifier for the screen/page |
| `state_id` | Which state was captured (default, empty, error, loading, etc.) |
| `viewport` | Viewport dimensions and DPR |
| `theme` | light, dark, high-contrast |
| `locale` | Language and direction (LTR/RTL) |
| `orientation` | portrait, landscape |
| `capture_tool` | playwright, xctest, compose-test, manual, etc. |
| `source_path` | Path to source file if code-review mode |
| `selector` or `node_id` | Element identifier for component-level evidence |
| `confidence_notes` | Any caveats about the evidence quality |

## State coverage requirements

The plugin should require explicit state coverage for screens where these states exist:

| State | Why |
|---|---|
| Default/populated | Baseline review |
| Empty | First-use experience |
| Loading | Perceived performance |
| Error | Trust and recovery |
| Success/confirmation | Task completion |
| Disabled | Understanding constraints |
| Focus-visible | Keyboard navigation |
| Hover/pressed | Desktop interaction |
| Validation errors | Form usability |
| Overflow/extreme content | Edge case robustness |
| Dark/light theme | Theme consistency |
| Reduced-motion | Accessibility |

If states cannot be provided, report says review coverage is incomplete.

## Capture strategies by platform

### Web (Playwright-based)

| Capability | Method |
|---|---|
| Full-page screenshot | `page.screenshot({ fullPage: true })` |
| Element screenshot | `locator.screenshot()` |
| Device emulation | `browser.newContext({ ...devices['iPhone 14'] })` |
| Dark mode | `colorScheme: 'dark'` in context |
| Reduced motion | `reducedMotion: 'reduce'` in context |
| Trace recording | `context.tracing.start()` ... `stop({ path })` |
| Video recording | `recordVideo: { dir: 'videos/' }` in context |
| Accessibility scan | `@axe-core/playwright` integration |
| DOM snapshot | `page.content()` or `page.accessibility.snapshot()` |
| Computed styles | `page.evaluate(() => getComputedStyle(el))` |
| Console/network | `page.on('console')`, `page.on('request')` |

### Apple (XCTest-based)

| Capability | Method |
|---|---|
| Full screen screenshot | `XCUIScreen.main.screenshot()` |
| Element screenshot | `element.screenshot()` |
| Element geometry | `XCUIElementAttributes` (frame, label, hasFocus, etc.) |
| Hierarchy snapshot | `XCUIElementSnapshot` |
| Accessibility audit | `app.performAccessibilityAudit(for:)` |

### Android (Compose/Espresso-based)

| Capability | Method |
|---|---|
| Screenshot test | Compose screenshot testing |
| Animation capture | `ComposeTestRule` clock control + `captureToImage()` |
| Accessibility check | Compose ATF checks (1.8.0+) |
| Configuration change | Espresso Device API (rotation, fold) |
| Jank detection | Android Studio trace-based |

## Evidence rules for findings

| Claim type | Minimum evidence |
|---|---|
| "Text clips at viewport X" | Screenshot at that viewport, or DOM overflow evidence |
| "Animation is excessive/sluggish" | Video or trace + timing metadata |
| "Reduced motion ignored" | Reduced-motion mode run or code evidence |
| "Focus is obscured" | Screenshot/trace + focus path evidence |
| "Target size too small" | Geometry data or platform metrics |
| "Layout shifts during load" | Trace/video or CLS metric |
| "Interaction feels slow" | Trace timings or video-supported delay |
| "Alignment is off" | Layout metrics or OCR box geometry (never screenshot-only assertion) |
| "Color contrast insufficient" | Computed color values + contrast ratio calculation |

## Confidence calibration

| Evidence level | Confidence |
|---|---|
| Automation + screenshot + metrics agree | Strong claim |
| Screenshot suggests issue, no corroborating data | "Likely issue -- needs verification" |
| Code analysis suggests issue, no runtime evidence | "Code-level concern -- verify at runtime" |
| Screenshot-only, spatial precision required | "Possible issue -- geometry measurement needed" |

## File-backed image reuse

Store artifacts on disk and reference by path. Do not repeatedly embed base64 images into conversation turns -- Anthropic vision docs recommend file references for repeated images in agentic workflows.
