# Product vs Brand Mode Selection, Comparison, and Stack Adaptation

> Provides a routing decision matrix between Product UI and Brand/Marketing UI, contrasts `@primer/react` and `@primer/react-brand` side by side, separates universal design principles from GitHub-proprietary identity, and explains how to apply Primer's lessons cleanly across Tailwind CSS, shadcn/Radix, plain CSS Custom Properties, MUI, and server-rendered stacks.

## Contents

- [Key takeaways](#key-takeaways)
- [Mode selection decision matrix](#mode-selection-decision-matrix)
- [Side-by-side comparison: Product UI vs Brand UI](#side-by-side-comparison-product-ui-vs-brand-ui)
- [Separating universal craft from GitHub brand identity](#separating-universal-craft-from-github-brand-identity)
- [Stack and framework adaptation guides](#stack-and-framework-adaptation-guides)
- [Preventing GitHub cloning and generic AI patterns](#preventing-github-cloning-and-generic-ai-patterns)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- **[Universal]** First classify the surface being designed as **Product Workspace**, **Brand / Marketing**, or **Editorial Hybrid (Docs / FTUX / In-app Upgrade)**—never let promotional marketing theatre hijack high-frequency workflows, and never squeeze narrative launches into cramped form chrome.
- **[Universal]** Primer's lasting value for *any* codebase lies in its structural architecture (3-tier tokens, non-linear contrast neutral scales, strict foreground/background pairings, viewport ranges vs breakpoints, scenario pattern mechanics, WCAG 2.2 AA accessibility discipline), **not** in copying `#0FBF3E` green, `#0969da` link blue, Mona Sans headings, or GitHub's Octicons/Octocat chrome.
- **[Primer]** `@primer/react` (Product) and `@primer/react-brand` (Marketing) are **separate packages with incompatible styling systems and tokens** (`--fgColor-*`/`--bgColor-*`/`--text-*-shorthand` vs `--brand-color-*`/`--brand-text-*`; Product deprecated `Box`/`sx` while Brand preserves `Box`). Never import both global stylesheets into the same CSS cascade.
- **[Universal]** Adopt Primer *without* installing Primer packages when a repository already runs Tailwind, shadcn/Radix, MUI, or vanilla CSS: translate the semantic concepts into the project's native token layer and primitives.

## Mode selection decision matrix

Choose the surface posture before selecting layout density, typography scale, motion cadence, or color ratio:

| Surface / User Intent | Primary Mode | Layout & Density Posture | Typographic & Motion Choice | Primer Reference Path |
| --- | --- | --- | --- | --- |
| SaaS app, admin console, IDE, CI/ops dashboard, settings, billing table | **Product UI** | Full/Split/Interstitial page (`<=1280px`), `1px` borders, `14px` dense UI body, zero decorative banners | System-first sans + mono, `100–300ms` transitions (`500ms` hard cap) | `references/product/01..10` |
| Product launch page, homepage, pricing showcase, campaign microsite, event hub | **Brand / Marketing UI** | 12-col Grid (`16/32/48px` gutters), Gridline/Flexsuite bands, generous whitespace, Hero -> Story -> Proof -> CTA -> Footer | Display scale (`40px`–`64px`), optical light-medium weights (`425–480`), `200–700ms` architectural easing | `references/brand/01..04` |
| Documentation portal, changelog, interactive FTUX onboarding, in-app plan upgrade | **Hybrid** | Calm Product readability (`<=80ch`, left-aligned prose) framed with clear editorial hierarchy and **bounded** onboarding cues (`<=2` inline callouts/cards, never blocking dialogs) | Product readable body (`14px`–`16px`) + optional Brand/Display opener (`--text-display-shorthand` `40px`) stepping down cleanly | `product/02,03,07,08` + `brand/02,04` |

Sources: `https://primer.style/product/`, `https://primer.style/brand/`, `https://brand.github.com/guides/getting-started/`, `references/product/01-principles-and-governance.md`, `references/brand/01-brand-identity-and-voice.md`.

## Side-by-side comparison: Product UI vs Brand UI

Understanding where `@primer/react` and `@primer/react-brand` diverge prevents invalid cross-package imports and clashing visual rhythms:

| Dimension | Primer Product (`@primer/react` + `@primer/primitives`) | Primer Brand (`@primer/react-brand` + `@primer/brand-primitives`) |
| --- | --- | --- |
| **Token prefix** | No prefix on functional tokens: `--fgColor-*`, `--bgColor-*`, `--borderColor-*`, `--text-*-shorthand*`, `--base-size-*`, `--control-*` | `--brand-*` on functional tokens: `--brand-color-canvas-*`, `--brand-color-text-*`, `--brand-text-size-*`, `--brand-heading-weight-*`; unprefixed `--base-*` base scales |
| **Neutral scale steps** | **14 steps (`0–13`)**: `0–6` backgrounds, intentional lightness jump `7–8` for borders (`>=3:1`), `9–13` text/icons | **6 steps** in print/Toolkit (`Gray 1–6`), **10 steps (`0–9`)** in `@primer/brand-primitives` (`#F2F5F3..#191F1B` light) |
| **Color modes & themes** | `ThemeProvider` supports `light`, `dark`, high contrast (`light_high_contrast`, `dark_high_contrast`), `dark_dimmed`, protanopia/deuteranopia & tritanopia variants (9 documented / 14 built) | `ThemeProvider` (`colorMode="light" \| "dark" \| "auto"`) via `data-color-mode`; supports per-section dark/light alternation |
| **Typography & weights** | System-first stack (`Mona Sans VF` optional); `14px` standard body (`body-medium`), `40px` max display (`display`); semibold (`600`) headings; leave `letter-spacing` alone | Webfont-led `Mona Sans` + optional `Hubot Sans` + `Mona Sans Mono`; body `16px` (`200`), display up to `64px` (`1000`); variable axis weights `400–550` (heading drops to `425` at `1012px+`); **bans `800`/`900`** |
| **`Box` & `sx` status** | **`Box` and `sx` prop DEPRECATED** in favor of CSS Modules + semantic CSS custom properties | **`Box` actively supported** as idiomatic layout wrapper alongside `Grid`, `Stack`, `Section` |
| **Radii & surfaces** | Crisp `3px` small, `6px` medium/default, `12px` large; calm `1px` borders (`--borderWidth-default`); non-sticky App Header | `4px` small, `8px` medium, `16px` large, `24px` xlarge; fixed `SubdomainNavBar`; Gridline borders (`variant="gridline"` / `"gridlines"` on `Tiles`) + directional dithered UI backdrops |
| **Motion speeds** | Utility-only: `100ms` micro, `200ms` short/exit, `300ms` enter max (`500ms` strict cap); zero bounce | Storytelling + UI: `80ms` controls, `200ms` faster, `300ms` fast, `600ms` default, `700ms` extended (`cubic-bezier(0.16, 1, 0.3, 1)`), `AnimationProvider` `100ms` stagger |
| **Iconography & art** | Strict **Octicons** (`12/16/24px` outlined `1.5px` stroke); minimal decorative illustration (avoid cluttering work views) | **Octicons** + gitline **Spot icons** + Contribution-square/Dither illustrations + Mona/Ducky mascots (when approved) |

Sources: `primer/primitives`, `primer/react`, `primer/brand`, `references/product/01..04`, `references/brand/02..04`.

## Separating universal craft from GitHub brand identity

Use this filter before adopting *any* rule from Primer or GitHub Brand into another product:

| Category | Apply Universally (`[Universal]`) | Keep GitHub-Specific (`[Primer]` / `[Brand]`) |
| --- | --- | --- |
| **Design philosophy** | Calm workspace restraint, clarity over cleverness, accessibility as baseline engineering, explicit UX tradeoffs | *"Ship to learn"*, Zen of GitHub aphorisms, staff governance labels (`Experimental` / `Staff-shipped`) |
| **Color architecture** | 3-tier tokens (`base -> functional -> component`), neutral contrast zones (`bg` / `3:1 border` / `4.5:1 text`), never pair muted-on-muted, non-color redundancy (`3:1` focus & marks) | GitHub Green (`#0FBF3E`), GitHub Blue (`#0969da`), `open`/`closed`/`done`/`sponsors` semantic hues, green-tinted grays (`#F2F5F3`) |
| **Typography** | Decouple heading level (`h1–h6`) from visual size, `<=80ch` measure, `rem` scales, lighter optical weights on huge display headings, short caption caps | `Mona Sans` / `Hubot Sans` / `Mona Sans Mono` pairings (note SIL OFL 1.1 allows free Mona Sans reuse, but don't default blindly if target product has its own typeface) |
| **Layout & patterns** | Coarse viewport ranges (`<768 / >=768 / >=1400`) + breakpoints, `320x256` reflow, `24px/44px` touch targets, left-side scroll panes, scenario patterns (inline single edit, promise-gated Copy `2s` check, non-blocking loading `<1s/1–3s/3–10s/>10s`, zero Toast bans, `<3` vs `>=3` validation error focus, type-to-confirm high-risk deletion) | `SubdomainNavBar`, `SplitPageLayout` exact defaults, 4-square contribution graph motif, halftone dithering, Flexsuite green accent divider line |
| **Brand assets & copy** | Plain active 7th-grade UI copy, sentence case buttons, `<=160`-char marketing river blurbs, clean UI demo screenshots | `Invertocat`, `Octocat` / `Mona` / `Ducky`, `GitHub` lockups, Octicons (MIT-licensed, but replace visually if host app uses Lucide/Heroicons/Material), GitHub trademarks |

Sources: `https://primer.style/product/`, `https://brand.github.com/`, `https://accessibility.github.com/`.

## Stack and framework adaptation guides

**Critical rule**: Do **not** install `@primer/react` or `@primer/react-brand` into a Tailwind, shadcn/Radix, MUI, Vue, or Rails-Hotwire project just to get "good design". Map the *principles* cleanly into the host stack:

### 1. Tailwind CSS + shadcn/ui / Radix UI (React)
- **Tokens**: Define your semantic `:root` / `[data-theme="dark"]` CSS custom properties cleanly (`--bg-default`, `--bg-muted`, `--bg-inset`, `--fg-default`, `--fg-muted`, `--fg-on-emphasis`, `--border-default`, `--border-muted`) paired in `tailwind.config` or Tailwind v4 `@theme`—avoid raw `bg-slate-100 text-slate-400` utilities scattered directly in JSX because `slate-400` on `slate-100` mimics Primer's banned `muted-on-muted` contrast trap.
- **Radix / shadcn behavior alignment**: Keep Radix `Dialog` mobile-friendly (`fullscreen` on `<768px` forms, max `<= 2` nested), avoid Radix `Toast` for critical or timed alerts (swap to inline banners/status messages + `@primer/live-region-element` or custom polite/assertive `aria-live` container), replace disabled-but-discoverable buttons with `aria-disabled="true"` + focusability rather than `disabled` dropping keyboard users.
- **Icons**: Stick to one consistent outline stroke icon family (`lucide-react` or `@primer/octicons-react` if GitHub-themed) matching a `1.5px`–`2px` uniform grid.

### 2. Vanilla CSS / CSS Modules / Web Components
- Implement Primer's exact 3-tier token split via plain `:root` + `[data-color-mode="dark"]` CSS custom properties + `@layer` cascades.
- Adopt `font: var(--text-body-shorthand-medium)` pattern (`font` shorthand tokens prevent mismatched `font-size` and `line-height`) and `@media (prefers-reduced-motion: reduce)` fallbacks on every transition rule.
- Use `<live-region>` (`@primer/live-region-element`, zero-framework Custom Element, MIT) or an equivalent single persistent `aria-live` node for dynamic announcements (copy confirmations, async loading completion).

### 3. Material UI (MUI) / Chakra / Mantine / Enterprise Libraries
- **Fight bloat & heavy elevation**: Override default MUI card shadows and oversized border radii with flat `1px` crisp borders (`borderColor-default`), reserving box shadows strictly for dropdowns (`zIndex 200`), overlays (`300`), and dialogs (`400`).
- **Form sanity**: Swap MUI/floating-label or placeholder-only inputs back to persistent external labels above inputs, post-submit validation (`>=3` errors -> top Error Summary Banner, `<3` -> focus first invalid field), and explicit Save/Cancel separation (`never` mix live-saving toggles cleanly with form submit buttons).

### 4. Server-rendered HTML / Rails / Django / HTMX
- Enforce semantic landmarks (`<header>`, `<nav>`, `<main>`, `<aside>`, `<footer>`), real `<a href>` for navigation vs `<button>` for actions, logical DOM order without CSS `order` hacks, `data-hotkey` alternatives gated cleanly per SC 2.1.4, and progressive enhancement via native `<details>`/`<summary>` or `<dialog>`.

Sources: `primer/primitives`, `primer/live-region-element`, `references/product/01..10`, `references/brand/02,04`.

## Preventing GitHub cloning and generic AI patterns

When an agent is asked to build a *non-GitHub* product or marketing site using this skill, audit the output for both **brand impersonation** and **generic AI UI slop**:

### Anti-cloning check (non-GitHub projects)
- **Never leak GitHub Green (`#0FBF3E`) or GitHub Blue (`#0969da`)** unless the target brand independently picked green/blue.
- **Never render Octocat, Mona, Ducky, Invertocat, contribution heatmap backgrounds, or `"Copilot"` copy** outside official GitHub integrations/docs.
- **Never imitate GitHub repository chrome** (`:owner / :repo` breadcrumbing, PR status pills `Open`/`Merged`, commit timeline tracks) inside non-developer CRM, finance, healthcare, or consumer UIs—adopt Primer's clean **table, form, split-pane, and banner discipline** instead.

### Anti-AI-slop check (both Product & Brand projects)
1. **No ambient purple/indigo gradient blobs or dark neon glows** behind generic cards (`rules.ts` flags this directly).
2. **No glassmorphism (`backdrop-filter: blur`) everywhere** or rounded-full pill CTAs masking poorly structured hierarchy.
3. **No raw unframed product screenshots** dropped directly onto a marketing canvas; frame with a deliberate neutral/textured container matching contrast rules.
4. **No weight `800`/`900` bloated typography** paired with sloppy `Lorem ipsum` or hollow hype taglines (*"Unlock the future of X"*).
5. **No Toast-spam**, hover-only Copy/Edit buttons, or decorative icons on every single heading/card.

Sources: `primer/brand` (`packages/mcp/src/review/rules.ts`), `references/brand/02-brand-color-typography-and-grids.md`, `references/product/05-accessibility.md`.

## Agent application notes

- **Step 1 — Pick mode**: Read user intent (`App workspace` vs `Marketing landing` vs `Hybrid docs/onboarding`) and load the appropriate reference folder (`references/product/*` vs `references/brand/*`).
- **Step 2 — Check stack**: Identify existing UI libraries (`package.json`, stylesheets). Only write `@primer/react` or `@primer/react-brand` JSX when the target repository already uses—or explicitly asks for—Primer; otherwise extract universal architecture into the project's native framework.
- **Step 3 — Enforce non-mixing**: When maintaining a real GitHub-ecosystem app *plus* marketing pages (for example Next.js marketing pages linking to an authenticated dashboard), keep `@primer/react-brand` and `@primer/react` isolated in separate routes/layouts rather than importing both CSS bundles onto one page.

## Gaps, caveats and source issues

- **Framework mappings outside Primer are derived adaptations (`[Inferred]`)**: Primer documentation covers React (`@primer/react`, `@primer/react-brand`), CSS utilities (`@primer/css`), and Rails (`primer_view_components` in maintenance mode); mappings to Tailwind, shadcn/Radix, MUI, and HTMX translate documented Primer invariants into idiomatic conventions rather than official Primer adapters.
- **No official hybrid theme package**: GitHub documents Product (`@primer/primitives`) and Brand (`@primer/brand-primitives`) separately; unified cross-surface transition patterns (`--text-display-shorthand` in Product or `Octicons` across both) bridge them manually without a single combined runtime stylesheet.
