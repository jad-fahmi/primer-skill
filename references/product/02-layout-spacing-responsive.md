# Primer Product Layout, Spacing, Sizing, and Responsive Design

> Covers Primer Product UI page anatomy, page types, viewport ranges, breakpoints, layout regions, spacing/size scales, control sizing, target sizes, and responsive adaptation rules.

## Contents

- [Key takeaways](#key-takeaways)
- [Layout philosophy and cognitive rationale](#layout-philosophy-and-cognitive-rationale)
- [Responsive foundations: viewport ranges vs. breakpoints](#responsive-foundations-viewport-ranges-vs-breakpoints)
- [Page anatomy and core page types](#page-anatomy-and-core-page-types)
- [Layout regions and responsive adaptations](#layout-regions-and-responsive-adaptations)
- [Spacing, container, and control sizing tokens](#spacing-container-and-control-sizing-tokens)
- [Touch targets, multimodal inputs, and reflow](#touch-targets-multimodal-inputs-and-reflow)
- [CSS grid and legacy utility classes](#css-grid-and-legacy-utility-classes)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Universal]` Layouts are composed of shapes parsed linearly in natural reading order; structure regions semantically so visual hierarchy matches screen-reader and keyboard flow.
- `[Universal]` Use **two levels of responsive abstraction**: coarse **viewport ranges** (`narrow < 768px`, `regular >= 768px`, `wide >= 1400px`) for column count and navigation structure, and **breakpoints** as neutral ruler marks for fine-tuning.
- `[Universal]` Never strip functionality on narrow screens: adapt multi-column layouts by drilling into separate views, opening auxiliary panes as bottom sheets, or stacking overviews—and avoid top-stacking long link lists above main content.
- `[Universal]` Support viewports down to **320px x 256px** (for 400% zoom reflow), provide at least **24px** (WCAG AA) and ideally **44px** (AAA) touch targets on mobile, and never hide controls behind `:hover` or fragile `pointer: coarse`/`fine` assumptions.
- `[Primer]` Primer's base grid uses **4px increments** (`--base-size-4`..`128`), functional `--stack-*` / `--space-*` tokens for layout density, and `--control-*` tokens (`xsmall` 24px through `xlarge` 48px, `medium` 32px default) for interactive controls.

## Layout philosophy and cognitive rationale

- **Linear parsing of shapes `[Universal]`**: Human vision processes color/texture in parallel but parses shapes sequentially. Because layouts are arrangements of shapes, clean alignment and predictable structural zones minimize cognitive friction.
- **Natural reading order `[Universal]`**: Organize regions to follow top-to-bottom, start-to-end reading flow and maintain logical DOM order (explicitly warn against using CSS `flex-order-*` or `-reverse` utilities to mask broken markup order, because keyboard and screen-reader order follows the DOM).
- **Calm and uncluttered `[Universal]`**: Respect attention by defaulting to clean surfaces and capping prose containers (`xlarge` 1280px page max-width) rather than edge-to-edge lines.
- **Shared mental models `[Universal]`**: Leverage conventions users already expect from operating systems, browsers, and the host product rather than inventing novel page structures.

Sources: [https://primer.style/product/getting-started/foundations/layout/](https://primer.style/product/getting-started/foundations/layout/); [https://primer.style/product/css-utilities/flexbox/](https://primer.style/product/css-utilities/flexbox/).

## Responsive foundations: viewport ranges vs. breakpoints

Primer separates high-level structural decisions (**viewport ranges**) from low-level component adjustments (**breakpoints**):

### Viewport ranges `[Universal]` / `[Primer]`

| Range | Media Query (`@primer/primitives`) | Columns | Purpose |
| --- | --- | --- | --- |
| `narrow` | `(max-width: calc(48rem - 0.02px))` (`< 768px`) | 1 | Single-column mobile and high-zoom reflow layouts. |
| `regular` | `(min-width: 48rem)` (`>= 768px`) | Up to 2 | Baseline for all desktop-capable patterns. |
| `wide` | `(min-width: 87.5rem)` (`>= 1400px`) | Up to 3 | Optional 3-column layouts when extra horizontal context helps. |
| `narrowLandscape` | `max-width: <1012px` & `max-height: <544px` & `landscape` | — | Short landscape screens (e.g., rotated phones). |

### Breakpoints (`@primer/primitives`) `[Primer]`

Primer treats breakpoints as neutral ruler marks rather than enforcing strict `min-width`-only directionality (while legacy `@primer/css` utilities remain `min-width` mobile-first):

| Token | Value (`rem` / `px`) | `@primer/css` Shorthand | `PageLayout` `Content` / `Container` Width |
| --- | --- | --- | --- |
| `--breakpoint-xsmall` | `20rem` (`320px`) | — | Interstitial page max-width (`320px`) |
| `--breakpoint-small` | `34rem` (`544px`) | `sm` (`>= 544px`) | Container `sm` |
| `--breakpoint-medium` | `48rem` (`768px`) | `md` (`>= 768px`) | `PageLayout` `width="medium"` (`768px`) |
| `--breakpoint-large` | `63.25rem` (`1012px`) | `lg` (`>= 1004px` in CSS grid note / `1012px` in hide utilities) | `PageLayout` `width="large"` (`1012px`) |
| `--breakpoint-xlarge` | `80rem` (`1280px`) | `xl` (`>= 1280px`) | `PageLayout` `width="xlarge"` (`1280px`) |
| `--breakpoint-xxlarge` | `87.5rem` (`1400px`) | — | `wide` viewport threshold (`1400px`) |

### Responsive container padding `[Primer]`

Padding belongs directly on the content or pane region—not the outer wrapper—so content max-width includes its padding (e.g., `1280px` with `24px` side padding gives `1232px` visual inner width):

- `xsmall` through `large` (`< 1280px`): **16px** content padding, **16px** pane padding.
- `xlarge` and `xxlarge` (`>= 1280px`): **24px** content padding, **16px** pane padding.
- *(Implementation note: `PageLayout.module.css` switches `--spacing-normal` from `--base-size-16` to `--base-size-24` at `>= 1012px`, while `PageLayout` content/pane slots default to `padding="none"` unless specified.)*

Sources: [https://primer.style/product/getting-started/foundations/layout/](https://primer.style/product/getting-started/foundations/layout/); [https://primer.style/product/primitives/size/](https://primer.style/product/primitives/size/); `primer/react` `packages/react/src/PageLayout/PageLayout.module.css`.

## Page anatomy and core page types

### Product page anatomy `[Primer]`

- **App header**: Topmost global bar containing global navigation/actions plus contextual elements (Context region, Local navigation) and optional system banners. **Never fixed/sticky**—it scrolls with the page so it does not consume limited vertical workspace.
- **Context region**: Shows current container identity (e.g., `:owner / :repository` or `:owner / Projects / :project`), **not** a full-page breadcrumb. Exclude custom buttons or dropdowns here; pair with selected Local navigation to complete location awareness.
- **App footer**: Secondary useful links and legal information kept below the fold on short pages. Omit only on dense, rich-interaction canvas/data tools where a bottom footer hinders work.

### Standard page types `[Universal]` / `[Primer]`

1. **Full page**: Horizontally centered header/content/pane container (typically max-width `xlarge` `1280px` to prevent overly wide reading lines). Best for standard product views, forms, and detail screens.
2. **Split page (`SplitPageLayout`)**: Full-bleed viewport split separating a scrollable side pane from main content.
   - **Rule**: Flush the primary split pane to the **start (left in LTR)** edge, never flush right, because a right-edge pane scrollbar collides visually and interactively with the browser's page scrollbar.
   - Best for settings, docs, filtering sidebars, and master-detail lists where scrolling a long list shouldn't lose context of the selected detail.
3. **Interstitial page**: Narrow focused container (typically `xsmall` `320px` max-width) for sign-in, password verification, or long-running blocking operations.

Sources: [https://primer.style/product/getting-started/foundations/layout/](https://primer.style/product/getting-started/foundations/layout/); [https://primer.style/product/components/page-layout/](https://primer.style/product/components/page-layout/); [https://primer.style/product/components/split-page-layout/](https://primer.style/product/components/split-page-layout/).

## Layout regions and responsive adaptations

### Core regions (`PageLayout` / `SplitPageLayout`) `[Primer]` / `[Universal]`

| Region | Semantic Role | Typical Content | Width / Sizing Behavior (`PageLayout.module.css`) |
| --- | --- | --- | --- |
| **Header** | Banner / page intro | Page title, optional summary, local nav, actions, metadata | Spans layout width above content/panes |
| **Content** | `<main>` | Primary task subject | `full` (`100%`), `medium` (`768px`), `large` (`1012px`), `xlarge` (`1280px`) |
| **Sidebar** | Full-height side region | Primary app/section navigation spanning beside header+content+footer | `start`/`end`, `small`/`medium`/`large` or resizable (`minWidth` 256px); supports `responsiveVariant="fullscreen"` |
| **Pane** | Auxiliary side region | Nav/filters (left) or metadata/details (right); sits beside Content only | `<768px`: `100%`; `768–1011px`: `240/256/256px`; `>=1012px`: `256/296/320px`; resizable (`minWidth` 256px) |
| **Footer** | `<footer>` | Secondary references or links | Spans layout width at bottom |

*Accessibility rule `[Universal]`*: If a pane scrolls independently (`overflow`), provide `aria-label` or `aria-labelledby` so keyboard and assistive-tech users can reach and identify the scrollable region.

### Narrow-viewport adaptation strategies (`< 768px`) `[Universal]`

1. **Split into separate pages (drill-in)**: For master-detail or settings views, render either the list pane or the detail content—never side-by-side—with a back link in the page header.
2. **Bottom sheet / fullscreen overlay**: Move secondary metadata, filters, or details behind a narrow-only trigger button that opens an overlay/sheet (`responsiveVariant="fullscreen"` on `PageLayout.Sidebar`).
3. **Vertical stack (with caution)**: Overview summaries may stack *above* content, and metadata/auxiliary blocks may stack *below* content. **Never stack a long list of navigation links above main content**, as it forces mobile users to scroll past navigation chrome before reaching the page's subject.

Sources: [https://primer.style/product/getting-started/foundations/layout/](https://primer.style/product/getting-started/foundations/layout/); [https://primer.style/product/components/page-layout/](https://primer.style/product/components/page-layout/); `primer/react` `packages/react/src/PageLayout/PageLayout.module.css`.

## Spacing, container, and control sizing tokens

### Base & Functional Spacing Scales `[Primer]`

- **Base size scale (`--base-size-*`)**: `2, 4, 6, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48, 64, 80, 96, 112, 128` px (exported in CSS as `rem` from `0.125rem` to `8rem`, plus `--base-size-negative-2`..`-48`).
- **Stack layout density tokens**:
  - `--stack-gap-condensed` / `--stack-padding-condensed`: `0.5rem` (`8px`)
  - `--stack-gap-normal` / `--stack-padding-normal`: `1rem` (`16px`)
  - `--stack-gap-spacious` / `--stack-padding-spacious`: `1.5rem` (`24px`)
  - *(Note: `@primer/react` `<Stack>` also supports `'none'` (`0`), `'tight'` (`--base-size-4`, 4px), and `'cozy'` (`--base-size-12`, 12px) alongside `'condensed'`, `'normal'`, and `'spacious'`, all accepting responsive breakpoint objects.)*
- **Unified `--space-*` functional scale (`primer/primitives` `src/tokens/functional/spacing/space.json5` & `spacing-scales.md`)**: Maps visual density rather than CSS property across `gap`, `padding`, and `margin`:
  `--space-xxs` (2px), `--space-xs` (4px), `--space-sm` (8px, default comfortable), `--space-md` (12px), `--space-lg` (16px), `--space-xl` (24px). *(Check target package version before emitting `--space-*`, as it was introduced separately from `--stack-*`.)*

### Control sizes & overlays (`@primer/primitives`) `[Primer]`

Use `--control-*` tokens for interactive inputs/buttons and `--stack-*` for surrounding layout containers—do not swap them (`DESIGN_TOKENS_GUIDE.md` & `@primer/mcp` rules).

| Control Tier | Height (`--control-*-size`) | Block Padding | Inline Padding (`condensed` / `normal` / `spacious`) | Gap |
| --- | --- | --- | --- | --- |
| `xsmall` | `1.5rem` (`24px`) | `2px` (`0.125rem`) | `4px` / `8px` / `12px` | `4px` |
| `small` | `1.75rem` (`28px`) | `4px` (`0.25rem`) | `8px` / `12px` / `16px` | `4px` |
| `medium` (default) | `2rem` (`32px`) | `6px` (`0.375rem`) | `8px` / `12px` / `16px` | `8px` |
| `large` | `2.5rem` (`40px`) | `10px` (`0.625rem`) | `8px` / `12px` / `16px` | `8px` |
| `xlarge` | `3rem` (`48px`) | `14px` (`0.875rem`) | `8px` / `12px` / `16px` | `8px` |

- **Control stack gaps**: `8px` (`0.5rem`) for `auto`/`condensed` across small/medium/large, `12px` (`0.75rem`) for medium/large `spacious`, `16px` (`1rem`) for small `spacious`. In pointer-responsive overrides: `coarse` widens `auto` gap to `12px` (medium/large) or `16px` (small).
- **Overlay dimensions**: Widths `xsmall` (192px), `small` (320px), `medium` (480px), `large` (640px), `xlarge` (960px); heights `small` (256px), `medium` (320px), `large` (432px), `xlarge` (600px); padding `normal` (`16px` / `12px` block) or `condensed` (`8px` / `4px` block); `--overlay-offset`: `4px`.

Sources: [https://primer.style/product/primitives/size/](https://primer.style/product/primitives/size/); [https://primer.style/product/components/stack/](https://primer.style/product/components/stack/); `primer/primitives` `src/tokens/functional/spacing/space.json5`, `DESIGN_TOKENS_GUIDE.md`, `contributor-docs/design/spacing-scales.md`; `primer/react` `packages/react/src/Stack/Stack.module.css`.

## Touch targets, multimodal inputs, and reflow

- **Minimum reflow viewport `[Universal]`**: Pages must remain readable and fully functional down to **320px width x 256px height** without horizontal loss or hidden features—this satisfies WCAG 2.2 Reflow (400% zoom on a 1280px screen).
- **Target sizes `[Universal]`**:
  - **24px minimum** required to satisfy WCAG 2.2 AA (`--control-xsmall-size` = 24px).
  - **44px recommended (WCAG AAA)** on mobile/touch contexts: Primer recommends using `large` controls (`40px`) or expanding the interactive hit area vertically/horizontally (`44px`) rather than keeping cramped `32px` targets on mobile.
- **Do not trust `pointer: coarse` / `pointer: fine` media queries `[Universal]`**: Modern devices support touch, mouse, keyboard, and voice simultaneously, and mobile browsers often fail to report attached keyboards/mice. Avoid hiding or removing desktop affordances based on pointer media queries alone.
- **Never gate essential content or actions behind `:hover` `[Universal]`**: Touch devices lack hover; ensure tooltip/hovercard information is reachable via links, clicks, or visible UI.
- **Respect OS/browser preferences `[Universal]`**: Honor `prefers-color-scheme`, `prefers-contrast`, `prefers-reduced-motion`, `forced-colors`, `inverted-colors`, and user default browser font sizes (`rem` scaling).

Sources: [https://primer.style/product/getting-started/foundations/responsive/](https://primer.style/product/getting-started/foundations/responsive/).

## CSS grid and legacy utility classes

Legacy `@primer/css` provides 12-column percentage grids (`.col-*`, `.col-sm-*`..`.col-xl-*`), margin/padding helpers (`.m-*`, `.p-*` on a 0–12 spacer scale: `0, 4, 8, 16, 24, 32, 40, 48, 64, 80, 96, 112, 128px`, with spacers 7–12 restricted on margins to vertical `mt`/`mb`/`my`), `.p-responsive`, `.hide-(sm|md|lg|xl)` (range-only hiding), `.d-*`, and `.sr-only` / `.show-on-focus`.
- **Important Flexbox browser caveats documented by Primer `[Universal]`**:
  1. Flex items can shrink past `min-content` and overflow text unless `flex-shrink: 0` (`.flex-shrink-0`) or `min-width: 0` is handled intentionally.
  2. `<button>`, `<fieldset>`, and `<summary>` have cross-browser bugs (notably Safari) when styled directly as `display: flex`; wrap inner content in a `<div>` with `display: flex` instead.
- **Programmatic show/hide rule `[Universal]`**: Toggle the native HTML `hidden` attribute (`element.hidden = !visible`) rather than adding/removing `.d-none` alongside `.d-inline` / `.d-flex`, avoiding CSS specificity/cascade conflicts.

Sources: [https://primer.style/product/css-utilities/grid/](https://primer.style/product/css-utilities/grid/); [https://primer.style/product/css-utilities/layout/](https://primer.style/product/css-utilities/layout/); [https://primer.style/product/css-utilities/flexbox/](https://primer.style/product/css-utilities/flexbox/); [https://primer.style/product/css-utilities/margin/](https://primer.style/product/css-utilities/margin/); [https://primer.style/product/css-utilities/padding/](https://primer.style/product/css-utilities/padding/).

## Agent application notes

- **When NOT using Primer packages `[Inferred]`**: Keep Primer's structural discipline—use `rem`-based spacing on a 4px rhythm, separate control sizing from container gap/padding scales, define a single `narrow / regular / wide` layout model, keep main content capped around ~1200–1280px for readable prose, flush left-hand navigation split panes left, and enforce 320px reflow + 24px/44px hit targets—using your project's own tokens or Tailwind/CSS variables.
- **When using `@primer/react` `[Primer]`**: Compose pages with `PageLayout` or `SplitPageLayout` and `Stack` + CSS Modules using `--stack-*` and `--control-*` variables. Do not generate deprecated `<Box>` or `sx` props.

## Gaps, caveats and source issues

- **Breakpoint `lg` minor inconsistency in `@primer/css` vs `@primer/primitives`**: Live `primitives/size` lists `--breakpoint-large` as `1012px` (`63.25rem`), whereas `css-utilities/grid` lists `lg` as `min-width: 1004px` (tied to a historical 980px + 24px padded page container) and `css-utilities/layout` `hide-lg` uses `768px–1011px`.
- **`--space-*` vs `--stack-*`**: `primitives/contributor-docs/design/spacing-scales.md` and `src/tokens/functional/spacing/space.json5` define `--space-xxs..xl` as a newer property-agnostic scale, while current `@primer/react` `Stack.module.css` and `DESIGN_TOKENS_GUIDE.md` still rely on `--stack-gap-*` / `--stack-padding-*` and `--base-size-*`.
- **Responsive `pointer: coarse` contradiction**: `primitives/size` includes `size-coarse.json5` / `size-fine.json5` pointer-reactive control-stack overrides, while the live `foundations/responsive` documentation explicitly warns against relying on `coarse`/`fine` media queries due to multimodal hardware. Favor explicit size props or generous spacing over pointer-query hiding.
