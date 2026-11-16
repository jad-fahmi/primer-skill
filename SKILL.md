---
name: primer-skill
description: Comprehensive product UI, brand/marketing design, design-token, and WCAG 2.2 AA accessibility skill distilled from Primer (primer.style) and GitHub Brand Toolkit (brand.github.com). Use when designing or reviewing web applications, SaaS dashboards, developer tools, marketing landing pages, design-token architectures, component APIs, UX microcopy, or accessibility audits—whether adapting universal UX/design principles to Tailwind/shadcn/CSS/other stacks or building natively with @primer/react and @primer/react-brand.
---

# Primer & GitHub Brand Design Skill

Distills **Primer** ([`primer.style`](https://primer.style/)) and the **GitHub Brand Toolkit** ([`brand.github.com`](https://brand.github.com/)) into reusable architecture, accessibility, token, UI-pattern, and brand-storytelling rules. Every recommendation is scoped clearly as `[Universal]` (portable product/brand craft), `[Primer]` (Primer Product UI / `@primer/react`), or `[Brand]` (GitHub marketing / `@primer/react-brand`) so agents elevate design judgment without forcing unrelated products to look like GitHub or importing incompatible libraries.

## Core Operating Philosophy

1. **Separate Product UI from Brand / Marketing UI**: Product workspaces demand calm density, crisp `1px` hierarchy, high task efficiency, `14px` UI copy, and sub-`300ms` mechanical transitions. Brand/marketing surfaces demand clear narrative pacing, editorial gridlines, expressive display scale, and `200ms–700ms` transitions. Never bleed promotional noise into dense app chrome or squeeze landing pages into cramped admin tables.
2. **Never clone GitHub onto unrelated apps**: For non-GitHub products, extract `[Universal]` craft (3-tier token modeling, 14-step contrast zones, strict foreground/background pairings, viewport ranges, form validation focus, accessible scenario patterns)—never GitHub Green (`#0FBF3E`), Invertocat, Mona/Ducky mascots, contribution-graph dithering, or repository status chrome (`Open`/`Merged`).
3. **Never force Primer dependencies into foreign stacks**: When a codebase already uses Tailwind, shadcn/Radix, MUI, Vue, Rails, or plain CSS Custom Properties, map Primer's token and pattern constraints into that native stack (`references/cross/01`). Only generate `@primer/react` or `@primer/react-brand` JSX when the target repository uses or requests Primer.
4. **Never mix `@primer/react` and `@primer/react-brand` stylesheets**: They define different token prefixes (`--fgColor-*`/`--bgColor-*` vs `--brand-*`), typography scales, radii, and layout rules (`Box` and `sx` are deprecated in Product yet idiomatic in Brand). Isolate Product and Marketing routes cleanly.
5. **Respect licensing, trademarks, and source status**: Primer code/docs are **MIT** and `Mona Sans` is **SIL OFL 1.1**, while `GITHUB®`, `INVERTOCAT`, `OCTOCAT®`, mascots, and downloadable Brand Guidelines remain proprietary GitHub property (`references/cross/03`). Do not use deprecated APIs (`Box`/`sx` in Product, `Bento`/`IDE` in Brand, Copilot 2025/2026 retired marks).

## Agent Workflow

Follow these five steps on every design, UI implementation, or design review task:

### 1. Classify Surface Mode
- **Product Workspace** (app UI, dashboard, settings, table, form flow) -> load `references/product/*`.
- **Brand / Marketing** (homepage, feature launch, pricing page, campaign, event) -> load `references/brand/*`.
- **Hybrid** (docs portal, in-app onboarding, upgrade surface) -> read [Product vs Brand & Adaptation](references/cross/01-product-vs-brand-and-adaptation.md) first, anchor prose readability (`<=80ch`) and Product tokens while borrowing restrained editorial structure (`<=2` onboarding cues).

### 2. Inspect Host Stack & Ownership
- Read `package.json` / styling config to decide whether to emit native `@primer/react`, `@primer/react-brand` (preferring bundled `llms.txt` / `@primer/brand-mcp` if installed), or stack-adapted native patterns (Tailwind / Radix / CSS Custom Properties / HTML).
- Confirm whether the product belongs to **GitHub** (allowing `[GitHub-only]` marks/palettes) or an external domain (enforcing target-brand palette/identity and `[Universal]` craft).

### 3. Plan Tokens, Layout, and Flows Before Coding
- Map colors via semantic roles (`default`, `muted`, `emphasis`, `onEmphasis`)—never raw hex literals or unbuffered `muted`-on-`muted` pairings ([Product Color & Tokens](references/product/04-color-system-and-tokens.md), [Brand Color & Grids](references/brand/02-brand-color-typography-and-grids.md)).
- Structure layouts for coarse viewport ranges (`<768px`, `>=768px`, `>=1400px`) and `320x256` reflow, natural DOM tab order, and `>=24px` / `44px` mobile targets ([Layout & Responsive](references/product/02-layout-spacing-responsive.md)).
- Select documented workflow behaviors cleanly: navigation hierarchy ([Navigation & IA](references/product/06-navigation-and-ia.md)), validation & onboarding limits ([Forms & Feedback](references/product/07-forms-feedback-and-onboarding.md)), honest loading/save/error/table patterns ([States & Data](references/product/08-states-loading-saving-errors-data.md)), and scenario flows ([Scenario Patterns & Components](references/product/09-scenario-patterns-and-components.md)).

### 4. Enforce Non-Negotiable Guardrails While Building
- **Accessibility**: Semantic `<button>` / `<a href>` / ordered `<h1>–<h6>`, visible `:focus-visible`, `aria-disabled="true"` for discoverable inactive controls instead of hidden `disabled`, polite single-node `aria-live`, `@media (prefers-reduced-motion: reduce)`, and zero Toasts, hover-only critical controls, or tooltip-on-static-text hacks ([Accessibility](references/product/05-accessibility.md)).
- **Marketing & Anti-AI-Slop**: Keep hero copy concise (`<=2` heading lines, `1` primary CTA per band), wrap product UI screenshots cleanly in contrasting backdrop frames (never bare `<img>`), avoid bloated `800`/`900` weights, ban purple/indigo glow blobs and arbitrary `backdrop-filter: blur` clutter, and replace placeholder copy ([Brand Components](references/brand/04-primer-brand-components-and-in-action.md), [Design Review Checklist](references/cross/02-design-review-checklist.md)).

### 5. Run Pre-Delivery Design Review
Evaluate all generated code or mockups against the [Design Review Checklist](references/cross/02-design-review-checklist.md) and report blocking defects vs polish improvements explicitly.

## Reference Library (Load Only Needed Modules)

### Product UI (`primer.style/product`, `primer.style/accessibility`, `primer.style/octicons`)
| File | Focus Area |
| --- | --- |
| [01-principles-and-governance.md](references/product/01-principles-and-governance.md) | Zen of GitHub, 5 UX pillars, lifecycle stages, `Box`/`sx` & `primer_view_components` status, API hygiene |
| [02-layout-spacing-responsive.md](references/product/02-layout-spacing-responsive.md) | `narrow`/`regular`/`wide`, breakpoints `320–1400px`, `PageLayout`/`SplitPageLayout`, `--base-size-*`/`--control-*`, reflow |
| [03-typography-and-visual-hierarchy.md](references/product/03-typography-and-visual-hierarchy.md) | Product font stacks, `--text-*-shorthand*`, heading outline vs size, `<=80ch`, borders, shadows, `--zIndex-*`, `<=300ms` UI motion |
| [04-color-system-and-tokens.md](references/product/04-color-system-and-tokens.md) | W3C JSON5 3-tier tokens, ADRs `000–011`, 14-step neutrals, 12 semantic roles, themes, AA/G174/`forced-colors`, pairings |
| [05-accessibility.md](references/product/05-accessibility.md) | WCAG 2.2 AA, 4x3 disability matrix, semantic ARIA, `aria-disabled`, `@primer/behaviors`, `@primer/live-region-element`, Toast/hover bans, 16-component matrix |
| [06-navigation-and-ia.md](references/product/06-navigation-and-ia.md) | Nav primitives (`NavList`, `TreeView`, `UnderlineNav`, `SegmentedControl`, `Breadcrumbs`, `ActionMenu`), responsive sidebars, `Dialog`/sheet rules |
| [07-forms-feedback-and-onboarding.md](references/product/07-forms-feedback-and-onboarding.md) | `FormControl`, post-submit/<3 vs >=3 error focus, `ConfirmationDialog` Cancel-focus, 6 banner tiers, `Popover`, onboarding limits |
| [08-states-loading-saving-errors-data.md](references/product/08-states-loading-saving-errors-data.md) | `<1s`/`1–3s`/`3–10s`/`>10s` loading, explicit-vs-auto save separation, `Blankslate`, degraded triage, DataViz & `DataTable` |
| [09-scenario-patterns-and-components.md](references/product/09-scenario-patterns-and-components.md) | 9 Scenario Patterns (`Copy`, `Create`, `Customize`, `Delegate`, `Delete`, `Edit`, `Filter`, `Search`, `View`), full 62-component catalog & status |
| [10-iconography-and-content-ux.md](references/product/10-iconography-and-content-ux.md) | Octicons `12/16/24px` specs, Octovisuals, 7th-grade UI copy, `RelativeTime`, archived `primer.style/native` (`gh`/Desktop/Mobile) |

### Brand & Marketing (`brand.github.com`, `primer.style/brand`)
| File | Focus Area |
| --- | --- |
| [01-brand-identity-and-voice.md](references/brand/01-brand-identity-and-voice.md) | Brand vs product positioning, 5 Brand Attributes, voice limits, logos/wordmarks, 2025/2026 Copilot retirements, co-branding & trademarks |
| [02-brand-color-typography-and-grids.md](references/brand/02-brand-color-typography-and-grids.md) | GitHub Green `#0FBF3E`, HEX/RGB/CMYK/PMS swatches, `--brand-*` tokens, `Mona Sans` calibrated `400–550` weights, 12-col Grid, Gridline/Flexsuite, AI tells |
| [03-brand-visuals-illustration-motion.md](references/brand/03-brand-visuals-illustration-motion.md) | Expression spectrum, 3 illustration pillars, contribution graph vs dithering rules, screenshot framing, Mona/Ducky limits, Brand Motion patterns & tokens |
| [04-primer-brand-components-and-in-action.md](references/brand/04-primer-brand-components-and-in-action.md) | `@primer/react-brand` setup & `@primer/brand-mcp`, 55+ components & 9 forms (`Bento`/`IDE` deprecated), `Card` vs `Pillar` rules, Social/Experiential/eBooks |

### Cross-Cutting Guides & Validation
| File | Focus Area |
| --- | --- |
| [01-product-vs-brand-and-adaptation.md](references/cross/01-product-vs-brand-and-adaptation.md) | Mode router, `@primer/react` vs `@primer/react-brand` diff matrix, `[Universal]` distillation, Tailwind/shadcn/CSS/MUI/Rails adaptation |
| [02-design-review-checklist.md](references/cross/02-design-review-checklist.md) | Pass/fail audit checklist for Product, Brand, Accessibility, Tokens, Copy, Forms/Data, and Anti-AI-Slop tells |
| [03-sources-licensing-and-gaps.md](references/cross/03-sources-licensing-and-gaps.md) | Full `415`-page + `32`-page + 11-repo audit ledger, MIT / SIL OFL 1.1 / Trademark compliance, archived/staff-only docs & live doc discrepancies |
