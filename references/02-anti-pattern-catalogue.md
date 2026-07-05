# Anti-Pattern Catalogue

Comprehensive catalogue of patterns that indicate poor execution, AI-generated output, or incomplete design. Cross-references the typescript-ui 108-tell catalogue, anti-slop design-patterns, and anti-slop frontend-patterns. Organized by category with specific detection methods.

**External deep references (agents MUST read these for full coverage):**

| Source | Path (if the plugin is installed) | Tells |
|---|---|---|
| typescript-ui anti-AI tells | `references/aesthetic/01-anti-ai-tells.md` in your `typescript-ui` plugin install (public mirror: `github.com/TheMizeGuy/typescript-ui-public`) | 108 tells with OKLCH replacements, specific CSS patterns, severity classifications |
| typescript-ui taste checklist | `references/aesthetic/04-taste-checklist.md` in the same plugin | Pre-ship audit checklist with verification methods |
| anti-slop design patterns | `skills/anti-slop/references/design-patterns.md` in your `anti-slop` plugin install (public: `github.com/TheMizeGuy/anti-slop`) | Visual design, CSS, functional, content anti-patterns |
| anti-slop frontend patterns | `skills/anti-slop/references/frontend-patterns.md` in the same plugin | CSS architecture, z-index, font loading, dark mode |

Both are optional companion plugins, not dependencies -- if you don't have them installed, skip these rows; the catalogue below still stands on its own. If installed via Claude Code's plugin cache, the path is version-named (e.g. `~/.claude/plugins/cache/<marketplace>/anti-slop/<version>/...`) and moves on every release -- resolve the current version at read time rather than hardcoding one.

---

## Top 10 Strongest AI Fingerprints (ranked by detection reliability)

| Rank | Fingerprint | CSS/code signature | Detection |
|---|---|---|---|
| 1 | Rounded card with uniform shadow + border on every container | `rounded-xl shadow-sm border` on every `Card` | Count Card components with identical border+shadow+radius treatment |
| 2 | Frosted glass sticky navigation bar | `bg-white/80 backdrop-blur-md border-b sticky top-0 z-50` | Check nav for backdrop-filter + sticky + opacity combo |
| 3 | Dashboard sidebar+header template | `w-64` dark sidebar + `h-16` header + search + bell + avatar | Compare layout against shadcn dashboard demo |
| 4 | All-caps tracking-wide subtitles/overlines | `text-xs uppercase tracking-wider` on table headers, section labels | Search for `uppercase tracking-wider` pattern |
| 5 | "Most Popular" pricing badge | `absolute -top-3 rounded-full` badge on middle tier | Check pricing sections for floating badges |
| 6 | Icon in tinted oval/square background | `bg-primary/10 w-12 h-12 rounded-lg` or `rounded-full` icon containers | Count colored background containers wrapping icons |
| 7 | Pulse skeleton with fractional widths | `animate-pulse bg-gray-200 w-3/4` stepped fractions | Search for `animate-pulse` with `w-1/2`, `w-3/4` |
| 8 | Black overlay modal | `bg-black/50` overlay + `max-w-md p-6 rounded-xl` dialog | Check modal/dialog overlay opacity and style |
| 9 | Responsive hero heading scale | `text-4xl sm:text-5xl lg:text-6xl font-bold tracking-tight` | Check hero h1 for three-step responsive scale |
| 10 | Four-column footer with standard sections | "Product / Company / Resources / Legal" + "All rights reserved" | Check footer column structure and text |

---

## 1. Color Tells (17)

| # | Pattern | Why AI does it | What to look for |
|---|---|---|---|
| C1 | Pure black background `#000000` | Tailwind `bg-black` | Check for `oklch(0 ...)` or `#000` -- should be warm/cool dark `oklch(0.12-0.14 ...)` |
| C2 | Pure white surface `#ffffff` | Tailwind `bg-white` | Check for `oklch(1 ...)` or `#fff` -- should be off-white `oklch(0.98-0.985 ...)` |
| C3 | Hashtag indigo `#6366f1` as primary | Tailwind `indigo-500` demo default | Check if primary hue is 250-285 (indigo/violet range) |
| C4 | Teal as secondary `#14b8a6` | Tailwind `teal-500` paired with indigo | Check for indigo+teal combination |
| C5 | Purple-to-pink gradient hero | `from-purple-500 to-pink-500` | Check hero sections for purple-pink gradients |
| C6 | Blue-to-teal gradient (Linear clone) | `from-blue-500 to-teal-400` | Check for blue-teal gradient mimicking Linear |
| C7 | Same neutral gray on every surface | One token set, no surface-aware tuning | Compare neutral tones across light/dark surfaces |
| C8 | Rainbow category palette | Red, orange, yellow, green, blue, purple for categories | Count distinct category colors; >4 is a smell |
| C9 | Glassmorphism translucent white over gradient | `backdrop-filter: blur()` over decorative gradient | Count `backdrop-filter` instances vs UI surface area |
| C10 | Untuned semantic status colors | Tailwind default green/red/yellow | Check contrast of status colors against actual surfaces |
| C11 | Decorative blur blobs | `bg-purple-300 rounded-full blur-3xl opacity-20` | Search for large blurred rounded-full elements |
| C12 | Identical shadow on every component | `shadow-sm` on every Card | Compare box-shadow values across components |
| C13 | Black modal overlay | `rgba(0,0,0,0.5)` | Check overlay color -- should be brand-tinted |
| C14 | Color-only meaning | Red border = error with no other indicator | Check if color is sole differentiator for states |
| C15 | Six-digit hex throughout | `#3b82f6` instead of OKLCH | Count hex values vs OKLCH values in styles |
| C16 | Mirror-inverted dark mode | Automated inversion, not designed | Compare dark mode for independent adjustments |
| C17 | One primary color for everything | Same blue for CTAs, links, charts, notifications | Check if accent is used identically across contexts |

## 2. Typography Tells (12)

| # | Pattern | Why AI does it | What to look for |
|---|---|---|---|
| T1 | Inter as primary font | Most-trained font in LLM corpus | Check `font-family` declarations |
| T2 | Roboto/Helvetica/Arial as primary | Default system fonts, zero risk | Check `font-family` |
| T3 | Newsreader + JetBrains Mono pairing | Recognizable AI output | Check font pairing |
| T4 | Geist Sans + Geist Mono unmodified | Every Vercel-deployed AI project | Check if Geist is customized |
| T5 | All-caps tracking-wide subtitles | `text-xs uppercase tracking-wider` | Search for uppercase+tracking combo |
| T6 | Display serif numerals (`01`, `02`, `03`) | Was distinctive in 2022, now generic | Check for decorative large numerals |
| T7 | Two-weight stack only (400 + 700) | AI only specifies regular + bold | Check font-weight variety |
| T8 | Default `letter-spacing: 0` on display headlines | AI never tunes tracking | Check display headlines for negative tracking |
| T9 | `<em>` italic when small-caps/weight would work | Default emphasis pattern | Check emphasis variety |
| T10 | Centered hero copy in narrow column | The Tailwind hero template | Check hero text alignment and width |
| T11 | Single-line giant headline | "Build better, faster" at 96px | Check hero headline structure |
| T12 | Body type at 14px | Density obsession | Check minimum font-size on body text |

## 3. Layout Tells (12)

| # | Pattern | Why AI does it | What to look for |
|---|---|---|---|
| L1 | Generic SaaS scaffold (hero->features->testimonials->CTA->footer) | Most-trained landing sequence | Check if page structure matches template regardless of product type |
| L2 | Centered everything | Easiest, looks "balanced" | Check if >80% sections use `text-align: center` + `mx-auto` |
| L3 | Bento grid as default | Hot since 2024, AI overuses | Check if grid is content-justified or decorative |
| L4 | Equal-width column grids (3-col, 4-col) | `grid-cols-3` reflex | Check for varied column widths |
| L5 | Cards everywhere as only container | shadcn `<Card>` default | Count card containers vs alternative grouping |
| L6 | Floating frosted nav (exact CSS) | Strongest single-component fingerprint | Match nav against `bg-white/80 backdrop-blur-md border-b sticky` |
| L7 | Section padding overload (`py-24` on every section) | Default section spacing | Check if all sections have identical padding |
| L8 | Marquee scrolling logo wall | "Trusted by" social proof reflex | Check for auto-scrolling logo bars |
| L9 | Stat counters animating from 0 | "Engagement" decoration | Check for counter animations |
| L10 | Generic dashboard layout (shadcn demo verbatim) | Dashboard demo reproduction | Compare against shadcn dashboard template |
| L11 | Alternating left-right feature showcase | Mechanical alternation | Check for L-R-L-R section pattern |
| L12 | SVG wave/curved section dividers | Decorative separator | Check for wave/curve SVGs between sections |

## 4. Component / Library Tells (12)

| # | Pattern | Why AI does it | What to look for |
|---|---|---|---|
| S1 | Default `border-input` color | shadcn default token never overridden | Compare `--input` to shadcn default |
| S2 | `rounded-md` (8px) everywhere | shadcn default radius | Check if all components use same radius |
| S3 | Default Card (border + p-6 + shadow-sm) | shadcn primitive unmodified | Compare Card styling to library defaults |
| S4 | Lucide icons unchanged throughout | shadcn ships with Lucide | Check if icon library is the only one |
| S5 | Button labeled "Button" or "Submit" | Placeholder copy | Search for generic button labels |
| S6 | Floating-label / placeholder-as-label inputs | Material holdover | Check input label patterns |
| S7 | Default toast styling (Sonner/shadcn) | No theme override | Compare toast to library defaults |
| S8 | Default browser scrollbar | No customization | Check scrollbar styling |
| S9 | Default dropdown arrow indicator | shadcn primitive | Check dropdown indicators |
| S10 | Skeleton on <300ms loads | Loading reflex | Check skeleton usage vs load time |
| S11 | "Most Popular" pricing badge | Pricing formula | Check pricing sections |
| S12 | Stats card with `$45,231.89` value | Verbatim shadcn dashboard demo | Check for known demo data |

## 5. Copy and Content Tells (10)

| # | Pattern | What to look for |
|---|---|---|
| W1 | Lorem ipsum or "the future of X" | Search for placeholder content |
| W2 | Vague benefit copy ("Streamline your workflow") | Check for non-specific value propositions |
| W3 | "Built for teams of every size" | Inclusivity hedge without specificity |
| W4 | "Powered by AI" / "AI-powered" everywhere | AI buzzword density |
| W5 | SaaS-speak ("collaborate seamlessly", "leverage", "supercharge") | Generic marketing verbs |
| W6 | Three-bullet feature description (title + verb-the-noun) | Template copy structure |
| W7 | "Trusted by [logos]" with no real logos | Social proof without substance |
| W8 | Fake testimonials ("Sarah Johnson, CEO at TechCorp") | Known AI-generated names |
| W9 | "Get started in seconds" with no actual onboarding | Speed reflex without substance |
| W10 | "Welcome back!" on every dashboard | Generic personalization |

## 6. Motion Tells (7)

| # | Pattern | What to look for |
|---|---|---|
| M1 | Every element fades-in on scroll | Framer Motion + IntersectionObserver on all sections |
| M2 | Spring-bounce on serious/destructive confirms | Single global motion preset for all interactions |
| M3 | Page transitions > 600ms | Transition duration on route changes |
| M4 | Marquee scrolling forever | Auto-scrolling without pause-on-hover |
| M5 | Hover scale-1.05 on every card | Identical hover treatment on all cards |
| M6 | All animations ignore `prefers-reduced-motion` | Missing `@media (prefers-reduced-motion)` |
| M7 | Animating layout-triggering properties | `width`, `height`, `top`, `left` animated |

## 7. Image and Asset Tells (6)

| # | Pattern | What to look for |
|---|---|---|
| I1 | Stock photography (smiling team in office) | Reverse-searchable stock images |
| I2 | Generic 3D blob shapes | Spline/Blender blobs without content relationship |
| I3 | Hand-drawn arrows pointing at CTA | "Conversion" gimmick elements |
| I4 | Unsplash gradient photos as backgrounds | Generic stock photography sections |
| I5 | Initials in colored circle as only avatar | `bg-muted rounded-full` with initials |
| I6 | Uncanny AI-generated imagery | Six-fingered hands, melted text, artifact faces |

## 8. Micro Tells (12)

| # | Pattern | What to look for |
|---|---|---|
| U1 | Trailing colons on form labels ("Email:") | Search for `:` in label text |
| U2 | "Enter your email" placeholder | Redundant label-restate in placeholder |
| U3 | Default/removed focus ring | `outline: none` without `:focus-visible` replacement |
| U4 | No `:focus-visible` distinction from `:focus` | Mouse and keyboard focus treated identically |
| U5 | `cursor: pointer` on non-interactive elements | Pointer cursor on static content |
| U6 | Tooltip on hover-only (no keyboard) | Tooltip without `:focus-visible` trigger |
| U7 | Modal with X AND "Close" button | Two close affordances |
| U8 | Spinner without context label | `<Spinner />` alone, no text |
| U9 | Error/success styled identically except color | Color-only state differentiation |
| U10 | Ambiguous date format (`1/2/26`) | Locale-naive numeric dates |
| U11 | Numbers without thousands separator | Raw `Number.toString()` output |
| U12 | Singular/plural unhandled ("1 items") | Naive string interpolation |

## 9. Deep Cuts: Subtle Tells (20)

These catch teams that overrode the obvious defaults but left fingerprints in second-order details.

| # | Pattern | Why it reads as AI |
|---|---|---|
| D1 | Border-radius 8px on every control | shadcn `--radius: 0.5rem` default. Pick 0, 2, 6, or 12 deliberately |
| D2 | `transition-all duration-200` on everything | Animates padding/border too, causing twitches. Specify properties |
| D3 | Shadow scale in arithmetic progression (sm/md/lg) | Tailwind defaults. Tune your own elevation scale |
| D4 | Every divider is `border-t border-border` (1px solid) | Vary: hairline gradient, dotted, whitespace-only |
| D5 | Every Avatar is a circle with initials in `bg-muted` | Use square with small radius, hexagon, asymmetric crop, or real photos |
| D6 | Tooltip fade 200ms with no offset | Should snap 50-100ms or rise on slight Y-translate |
| D7 | All cards same hover treatment (shadow lift + scale) | Cards leading to different actions should have different hovers |
| D8 | All form errors below input in red text | Vary: inline icon+tooltip, panel for form-level, modal for blocking |
| D9 | Lucide `Loader2` with `animate-spin` | Custom branded loader, shimmer skeleton, or progressive reveal |
| D10 | All section h2 same size with same `mb-4` to subhead | Vary section openings: quote, image, stat, different spacing |
| D11 | Content max-width `max-w-7xl` | Custom max-width: editorial `65ch`, dashboard full-bleed, marketing context-dependent |
| D12 | Container padding always `px-4 sm:px-6 lg:px-8` | Tailwind responsive container reflex. Pick single value + container queries |
| D13 | Every section ends with centered CTA button | Marketing reflex. Some sections lead to next section, not action |
| D14 | Hero subhead `text-xl text-muted-foreground max-w-2xl mx-auto` | Centered narrow-column subhead is strongest hero formula tell |
| D15 | Footer 4 columns "Product / Company / Resources / Legal" | 80%+ AI sites have this footer. Match to actual sitemap |
| D16 | Logo left, links centered, CTA right nav | Tailwind nav template. Vary alignment |
| D17 | Search input with Search icon left, "Search..." placeholder | Vary placeholder text, add keyboard shortcut hint |
| D18 | Settings page as Tabs component | Sidebar nav or vertical scroll with anchors may be better |
| D19 | Date picker is always a calendar popover | If users pick "today" or "next week", offer those as buttons first |
| D20 | Default `EmptyState` component (icon + title + description + button) | Empty state should have product voice, not component-library voice |

## 10. Specific Visual Tells (user-flagged)

| # | Pattern | Description | Detection |
|---|---|---|---|
| V1 | **Oval/pill icon backgrounds** | Icons wrapped in `rounded-full bg-primary/10 p-3` or `bg-muted rounded-lg p-2.5` creating identical tinted ovals/pills behind every icon in a feature grid | Search for `rounded-full` or `rounded-lg` wrapping icon components with tinted backgrounds |
| V2 | **Edge highlight line on one side** | Subtle gradient or solid color strip on one edge of a card/section (`border-l-4 border-primary`, left-colored border, or CSS gradient creating a light highlight line along top/left edge) | Search for `border-l-*`, `border-t-*` with accent colors, or gradient borders that create one-sided highlights |
| V3 | **Colored left border on sections** | `border-l-4 border-blue-500` or similar colored left border on info boxes, callouts, quotes -- the "colored-left-border section" pattern | Search for `border-l-*` with color classes |
| V4 | **Badge pill above hero heading** | Small colored pill `rounded-full px-3 py-1 text-sm bg-primary/10` above headline with "Introducing...", "New", "AI-Powered" | Check hero sections for small pill badges above h1 |
| V5 | **Gradient text on hero words** | `bg-gradient-to-r bg-clip-text text-transparent` on select words in headline | Search for `bg-clip-text text-transparent` pattern |
| V6 | **Two-button CTA group** | One filled + one ghost button side by side, always "Get Started" + "Learn More" | Check hero CTAs for this exact dual-button pattern |
| V7 | **Social proof notification toast** | "John from NYC just signed up 2 minutes ago" fake-activity popup | Search for timed notification toasts with activity messages |
| V8 | **Chat widget bubble** | `fixed bottom-4 right-4 rounded-full w-14 h-14` with chat icon, no real chat system | Check for fixed bottom-right circular buttons |
| V9 | **Dot/grid background pattern** | `radial-gradient(circle, #e5e7eb 1px, transparent 1px)` behind hero | Search for radial-gradient dot patterns as backgrounds |
| V10 | **Identical testimonial cards** | 3 cards, 5 yellow stars, 2-3 sentence quotes, circular avatar, "CEO at TechCorp" | Check testimonial structure for template uniformity |

## 11. Platform-Specific Anti-Patterns

### Web (15)

| # | Pattern |
|---|---|
| P1 | Viewport media queries for component layout (use container queries) |
| P2 | Fixed pixel breakpoints when `clamp()` works |
| P3 | `100vh` on mobile causing content behind browser chrome |
| P4 | Disabling zoom (`user-scalable=no`) |
| P5 | `float` for layout (non-text-wrap use) |
| P6 | `!important` overrides instead of fixing specificity |
| P7 | Excessive CSS nesting (`.page .content .section .card .header .title span`) |
| P8 | `div onclick` instead of `button` |
| P9 | Heading levels skipped for size (h1 to h3) |
| P10 | Missing `lang` attribute on `<html>` |
| P11 | No skip navigation link |
| P12 | `tabindex` > 0 (breaks natural tab order) |
| P13 | CSS `order` breaking tab order without `tabindex` fix |
| P14 | `title` attribute used instead of `aria-label` for tooltips |
| P15 | Dynamic content inserted without `aria-live` announcement |

### Apple (10)

| # | Pattern |
|---|---|
| A1 | Custom navigation bar instead of NavigationStack |
| A2 | Non-system fonts without Dynamic Type support |
| A3 | Hard-coded colors instead of semantic system colors |
| A4 | Tap targets below 44pt |
| A5 | Custom tab bar without proper accessibility traits |
| A6 | Missing VoiceOver labels on interactive controls |
| A7 | No `@Environment(\.accessibilityReduceMotion)` check |
| A8 | Phone layout stretched to iPad without adaptation |
| A9 | Missing haptic feedback on significant interactions |
| A10 | Custom modal overlay instead of `.sheet()` / `.fullScreenCover()` |

### Android/Material (10)

| # | Pattern |
|---|---|
| AM1 | Phone-only layout with no adaptive behavior for tablets/foldables |
| AM2 | Bottom navigation on expanded window widths (should be rail/drawer) |
| AM3 | Touch targets below 48dp |
| AM4 | Custom components replacing Material defaults without preserving semantics |
| AM5 | Ignoring window size class changes on configuration change |
| AM6 | No Compose accessibility semantics on custom composables |
| AM7 | Configuration change causing state loss or crash |
| AM8 | Standard Material components with semantics stripped |
| AM9 | Bottom sheet without proper gesture/keyboard/back handling |
| AM10 | FAB for non-primary actions |

---

## Severity Classification

| Severity | Examples |
|---|---|
| **CRITICAL** | Top 3 fingerprints unmodified, hex-not-OKLCH, Inter/Roboto as primary, default shadcn unmodified, lorem ipsum, ignored `prefers-reduced-motion`, missing focus ring, icon-only buttons without labels |
| **HIGH** | Pure black/white, hashtag indigo+teal, default gradients, centered-everything layout, frosted nav verbatim, generic dashboard, SaaS-speak, "Most Popular" badge, placeholder-as-label |
| **MEDIUM** | Untuned neutrals, glassmorphism overuse, uniform shadow, mirror dark mode, all-caps overlines, bento-as-default, padding overload, alternating L-R features, default toast |
| **LOW** | Blur blobs, black overlay, logo marquee, stat counters, wave dividers, slow page transitions, hover-scale-everywhere |

**Total catalogue: 153 patterns** (17 color + 12 typography + 12 layout + 12 component + 10 copy + 7 motion + 6 image + 12 micro + 20 deep cuts + 10 visual tells + 15 web + 10 Apple + 10 Android = 153 unique; the Top 10 ranked list is a cross-cutting subset that duplicates entries already counted in the categories above)
