# Primer Product Typography, Elevation, Borders, and Motion Utilities

> Covers Primer Product UI typography tokens, font stacks, semantic heading discipline, readability rules, elevation/shadow-to-z-index alignment, borders, and UI motion rules.

## Contents

- [Key takeaways](#key-takeaways)
- [Product typography system and tokens](#product-typography-system-and-tokens)
- [Typographic hierarchy and readability guidelines](#typographic-hierarchy-and-readability-guidelines)
- [Semantic headings vs. visual styles](#semantic-headings-vs-visual-styles)
- [Elevation, shadows, borders, and z-index layering](#elevation-shadows-borders-and-z-index-layering)
- [Product UI motion and animation rules](#product-ui-motion-and-animation-rules)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Universal]` **Never rely on color alone for hierarchy**: establish structure primarily through type scale, weight, spacing, and layout grouping.
- `[Universal]` **Decouple semantic heading levels (`h1`–`h6`) from visual scale**: never skip or reorder heading tags just to achieve a smaller or larger font size; style the semantic element with tokens/classes instead.
- `[Primer]` **Prefer `font: var(--text-*-shorthand*)` declarations** so `font-weight`, `font-size`, `line-height`, and `font-family` stay synchronized on a 4px grid with `rem` units (for browser zoom accessibility).
- `[Universal]` **Align shadows and z-index intentionally**: rest surfaces stay flat (`z-index: default` + `1px` border), dropdowns/menus use floating small/medium shadows (`z-index: dropdown`), modals use large shadows (`z-index: modal`), and skip links stay highest (`z-index: skipLink`).
- `[Universal]` **Restrained utility motion**: UI transitions should stay at or below **300ms** (`100ms` micro for hover/focus, `200ms` short for state toggles, `300ms` medium for overlays entering), never exceed `500ms`, and always disable or swap under `prefers-reduced-motion: reduce`.

## Product typography system and tokens

### Font stacks (`@primer/primitives`) `[Primer]`

| Token | Resolved Stack | Intended Use |
| --- | --- | --- |
| `--fontStack-system` / `--fontStack-sansSerif` / `--fontStack-sansSerifDisplay` | `'Mona Sans VF', -apple-system, BlinkMacSystemFont, 'Segoe UI', 'Noto Sans Backtick Fix', 'Noto Sans', Helvetica, Arial, sans-serif, 'Apple Color Emoji', 'Segoe UI Emoji'` | System-first sans-serif prioritizing performance while loading `Mona Sans VF` where available. |
| `--fontStack-monospace` | `ui-monospace, SFMono-Regular, SF Mono, Menlo, Consolas, Liberation Mono, monospace` | Code blocks, inline code, terminals, SHAs, and tabular technical data. |

*Note `[Universal]`*: Product UI defaults to system sans-serif stacks for instant zero-download rendering and OS familiarity, contrasting with Brand/Marketing UI where custom expressive `Mona Sans` and `Mona Sans Mono` webfont treatments carry stronger visual personality.

### Typographic scale & shorthand tokens (`@primer/primitives`) `[Primer]`

All sizes use `rem` (scaling cleanly with browser default font preferences) and unitless line heights tuned to align with the 4px grid.

| Role / Token (`--text-*`) | Font Size | Line Height | Weight | Font Stack | Notes |
| --- | --- | --- | --- | --- | --- |
| `display` (`--text-display-shorthand`) | `2.5rem` (40px) | `1.375` | `500` (medium) | `sansSerifDisplay` | Hero / brand-to-product transitions only; drop to `title-large` on narrow viewports. |
| `title-large` (`--text-title-shorthand-large`) | `2rem` (32px) | `1.5` | `600` (semibold) | `sansSerifDisplay` | Major page titles. |
| `title-medium` (`--text-title-shorthand-medium`) | `1.25rem` (20px) | `1.625` | `600` (semibold) | `sansSerifDisplay` | Primary section headings (`PageHeader` etc.). |
| `title-small` (`--text-title-shorthand-small`) | `1rem` (16px) | `1.5` | `600` (semibold) | `sansSerif` | Compact section or card headers. |
| `subtitle` (`--text-subtitle-shorthand`) | `1.25rem` (20px) | `1.625` | `400` (normal) | `sansSerifDisplay` | Supporting subtitles beneath titles. |
| `body-large` (`--text-body-shorthand-large`) | `1rem` (16px) | `1.5` | *(unset in shorthand / inherit)* | `sansSerif` | Long-form reading, spacious prose. |
| `body-medium` (`--text-body-shorthand-medium`) | `0.875rem` (14px) | `1.5` | *(unset / default UI)* | `sansSerif` | Standard dense GitHub product UI body size. |
| `body-small` (`--text-body-shorthand-small`) | `0.75rem` (12px) | `1.625` | *(unset)* | `sansSerif` | Dense metadata, secondary descriptions. |
| `caption` (`--text-caption-shorthand`) | `0.75rem` (12px) | `1.25` | `400` (normal) | `sansSerif` | **Short labels/captions only—never multi-line body copy** (fails readability). |
| `codeBlock` (`--text-codeBlock-shorthand`) | `0.8125rem` (13px) | `1.5` | `400` (normal) | `monospace` | Multi-line code snippets and diffs. |
| `codeInline` (`--text-codeInline-shorthand`) | `0.9285em` | *(inherits)* | `400` (normal) | `monospace` | Inline code proportional to surrounding body size (`0.9285em`). |

**Strict token constraints (`DESIGN_TOKENS_GUIDE.md` & `@primer/mcp`) `[Primer]`**:
- Only `body` and `title` accept `-small`, `-medium`, `-large` suffixes; **never** append size suffixes to `display`, `subtitle`, `caption`, `codeBlock`, or `codeInline`.
- Always use `font: var(--text-*-shorthand*)` rather than mixing raw `font-size` and raw `line-height: 1.5`.
- Use `--base-text-weight-light` (`300`), `normal` (`400`), `medium` (`500`), or `semibold` (`600`)—never arbitrary raw weights like `700`/`800`/`900`. Note also from legacy `@primer/css`: avoid light weights (`300`) below `20px` (`f3`) because thin strokes lose legibility at small sizes.

Sources: [https://primer.style/product/primitives/typography/](https://primer.style/product/primitives/typography/); [https://primer.style/product/getting-started/foundations/typography/](https://primer.style/product/getting-started/foundations/typography/); [https://primer.style/product/css-utilities/typography/](https://primer.style/product/css-utilities/typography/); `primer/primitives` `src/tokens/functional/typography/typography.json5` & `DESIGN_TOKENS_GUIDE.md`.

## Typographic hierarchy and readability guidelines

- **Don't rely on color for hierarchy `[Universal]`**: Establish prominence using size, weight, spacing, and spatial grouping rather than bright or varied text colors; color carries semantic state (errors, links, status) and becomes noisy if overused for emphasis.
- **Line length (`<= 80ch`) `[Universal]`**: Keep prose lines around 80 characters or fewer (citing W3C Page Structure guidance) so readers do not lose track while scanning back to the next line.
- **Left-aligned, ragged right `[Universal]`**: Default to start/left-aligned text in product UI; avoid justified text (creates uneven word rivers) and avoid centered or right-aligned multi-line prose.
- **Leave letter-spacing untouched `[Primer]`**: Archived `foundations/typography.mdx` explicitly advises against altering `letter-spacing` in product UI outside rare exceptions; rely on font metrics and optical sizing instead.
- **Word breaking & truncation `[Universal]`**: Use `word-break: break-word` / `overflow-wrap: break-word` for long unbroken strings (e.g., URLs, SHAs) before falling back to character-level `break-all`. Reserve `<Truncate>` (`maxWidth`, optional `expandable` on hover) for constrained metadata where clipping doesn't hide critical meaning—avoid trapping critical full values exclusively inside hover-only truncation on touch devices.

Sources: [https://primer.style/product/getting-started/foundations/typography/](https://primer.style/product/getting-started/foundations/typography/); [https://primer.style/product/css-utilities/typography/](https://primer.style/product/css-utilities/typography/); [https://primer.style/product/components/truncate/](https://primer.style/product/components/truncate/); `primer/design` `content/foundations/typography.mdx` (archived).

## Semantic headings vs. visual styles

- **Order HTML headings strictly by document outline (`h1` -> `h2` -> `h3`) `[Universal]`**: Never pick `<h4>` instead of `<h2>` just because `<h2>` looks too large, and never skip heading levels.
- **Separate semantics from presentation `[Universal]` / `[Primer]`**:
  - In `@primer/react`: `<Heading as="h3" variant="small">` sets the semantic `<h3>` tag while choosing the visual title scale (`large`, `medium`, `small`). Similarly `<Text as="p" size="small" weight="semibold">` handles non-heading emphasis without misusing heading tags.
  - In `@primer/css`: use `.h1`–`.h6` or `.f1`–`.f6` utility classes on semantic elements (or `.h2` visual scale on an `<h3>`) rather than swapping tag levels.
  - Never use `<Heading>` purely for visual styling of non-heading UI text—use `<Text>` instead.

Sources: [https://primer.style/product/getting-started/foundations/typography/](https://primer.style/product/getting-started/foundations/typography/); [https://primer.style/product/components/heading/](https://primer.style/product/components/heading/); [https://primer.style/product/components/text/](https://primer.style/product/components/text/); `primer/react` `packages/react/src/Heading/Heading.module.css`.

## Elevation, shadows, borders, and z-index layering

Primer's product UI relies on crisp `1px` (`--borderWidth-thin` / `--borderWidth-default`, `0.0625rem`) borders and restrained shadows rather than heavy drop-shadow layering:

### Borders and radii (`@primer/primitives`) `[Primer]`

- **Widths**: `thin` / `default` (`1px`), `thick` (`2px`), `thicker` (`4px`); inset border shadow helpers `--boxShadow-thin` / `thick` / `thicker`.
- **Corner radii**: `--borderRadius-small` (`3px`), `--borderRadius-medium` / `default` (`6px`), `--borderRadius-large` (`12px`), `--borderRadius-full` (`9999px` — for pills/circular avatars, not standard buttons).
- **Focus outlines**: Always apply `--outline-focus-width` (`2px`) and `--outline-focus-offset` (`-2px` or `var(--focus-outline-offset)`) on `:focus-visible`.

### Paired Shadow and Z-Index Hierarchy `[Universal]` / `[Primer]`

Never invent arbitrary `z-index: 9999` values or decorative large shadows on static cards. Align elevation level to stacking layer (`primitives` `z-index.json5`, `shadow.json5`, `DESIGN_TOKENS_GUIDE.md`):

| Layer / Z-Index Token | Numeric Value | Paired Shadow Token | Appropriate UI Elements |
| --- | --- | --- | --- |
| `--zIndex-behind` | `-1` | None | Decorative canvas behind siblings (only if stacking context chain is controlled; never to hide content). |
| `--zIndex-default` | `0` | `--shadow-inset` (pressed/inputs) or `--shadow-resting-xsmall`/`small`/`medium` | Flat content, inputs, buttons (`resting.small`), content cards (`resting.medium`). Prefer borders over shadows for normal separation. |
| `--zIndex-sticky` | `100` | Subtle border or resting shadow | Sticky headers, sidebars, table headers (`position: sticky` + opaque background). |
| `--zIndex-dropdown` | `200` | `--shadow-floating-small` / `medium` | Dropdowns, `ActionMenu`, `SelectPanel`, autocomplete lists. |
| `--zIndex-overlay` | `300` | `--shadow-floating-medium` / `xlarge` | Overlay backdrops, drawers, side sheets, bottom sheets. |
| `--zIndex-modal` | `400` | `--shadow-floating-large` | Modal dialogs (`Dialog`, `ConfirmationDialog`) that trap focus. |
| `--zIndex-popover` | `500` | `--shadow-floating-small` / `medium` | Tooltips, popovers, hovercards appearing above modals. |
| `--zIndex-skipLink` | `600` | None / focus outline | Accessibility "Skip to content" link—must always outrank modals and popovers. |

- **Prefer `isolation: isolate` `[Universal]`**: Create local stacking contexts instead of escalating global z-index numbers.
- **Large/XL shadows sparingly `[Primer]`**: `box-shadow` utilities explicitly note large and extra-large diffused shadows are primarily for marketing shots or floating UI overlays, not ordinary product cards.

Sources: [https://primer.style/product/primitives/size/](https://primer.style/product/primitives/size/); [https://primer.style/product/css-utilities/borders/](https://primer.style/product/css-utilities/borders/); [https://primer.style/product/css-utilities/box-shadow/](https://primer.style/product/css-utilities/box-shadow/); `primer/primitives` `src/tokens/functional/size/z-index.json5`, `src/tokens/functional/shadow/shadow.json5`, `DESIGN_TOKENS_GUIDE.md`.

## Product UI motion and animation rules

Unlike marketing surfaces (covered in `references/brand/03-brand-visuals-illustration-motion.md`), product UI motion exists purely to **communicate state changes without slowing workflow**:

- **Strict timing caps (`primitives` `motion.json5` & `DESIGN_TOKENS_GUIDE.md`) `[Primer]` / `[Universal]`**:
  - `--motion-duration-micro` (**100ms**): hover, focus ring, color shifts (`--motion-easing-hover` `[0.25, 0.1, 0.25, 1]`).
  - `--motion-duration-short` (**200ms**): expand/collapse, toggles, exit transitions (`--motion-easing-move` `[0.6, 0, 0.2, 1]` or `--motion-easing-exit` `[0.7, 0.1, 0.75, 0.9]`).
  - `--motion-duration-medium` (**300ms**): modals, dropdowns, tooltips entering (`--motion-easing-enter` `[0.3, 0.8, 0.6, 1]` — decelerates into place). **Maximum duration for routine UI interactions.**
  - `--motion-duration-long` (**500ms**): large multi-step layout shifts only; **never exceed 500ms** or use for normal buttons/toggles.
  - `--motion-easing-linear` (`[0, 0, 1, 1]`): continuous progress bars and spinners only.
- **Composite transition tokens `[Primer]`**: Use `--motion-transition-hover`, `--motion-transition-stateChange`, `--motion-transition-enter`, and `--motion-transition-exit` (or `--base-duration-*` / `--base-easing-*`) on the **base selector**, not only inside `:hover`, so reverse transitions remain smooth. Notice `exit` (200ms) is deliberately faster than `enter` (300ms) so dismissals feel snappy.
- **Accessibility & restraint `[Universal]`**:
  - Always include `@media (prefers-reduced-motion: reduce)` with instant fallbacks (`transition: none` or `opacity`-only subtle feedback).
  - Never animate purely for decoration, never loop indefinitely without user control (except active loading indicators), and never rely solely on animation to communicate status.
- **Legacy animation utilities (`@primer/css`) `[Primer]`**: `.anim-fade-in`, `.anim-fade-out`, `.anim-fade-up`, `.anim-fade-down` (inside `overflow: hidden`), `.anim-scale-in` (menus), `.anim-grow-x`, `.anim-pulse`, `.anim-hover-grow`, `.anim-rotate`.

Sources: [https://primer.style/product/css-utilities/animations/](https://primer.style/product/css-utilities/animations/); `primer/primitives` `src/tokens/functional/motion/motion.json5`, `src/tokens/base/motion/easing.json5`, `src/tokens/base/motion/timing.json5`, `DESIGN_TOKENS_GUIDE.md`; `primer/react` `packages/mcp/src/primitives.ts`.

## Agent application notes

- **Type & spacing transfer `[Inferred]`**: When building a non-Primer product, keep Primer's typographic ratios (14px dense app UI body / 16px comfortable prose, 12px short captions only, 1.5 default line height, `<= 80ch` line length, clear `title > subtitle > body > caption` stepping) and semantic elevation stack (flat 1px-bordered surfaces, subtle resting shadows, elevated overlays with matching z-index tiers, <= 200–300ms transitions with `prefers-reduced-motion`).
- **Avoid generic AI visual clichés `[Universal]`**: Skip ultra-heavy `800`/`900` weights, arbitrary drop shadows on every container, mismatched heading levels chosen for font size, and slow `500ms+` springy UI transitions that make productivity interfaces feel sluggish.

## Gaps, caveats and source issues

- **`Heading` doc vs. implementation**: `live/product__components__heading.md` states `Heading` renders an `h2` "without any default styling", whereas `Heading.module.css` applies `margin: 0`, `font-size: var(--text-title-size-large)` (2rem), and `font-weight: var(--base-text-weight-semibold)` (600) by default (unless overridden by `variant="large"|"medium"|"small"`).
- **Incomplete `Text` live doc**: `live/product__components__text.md` contains a placeholder note stating the `size` and `weight` example won't work until `@primer/react` upgrades, even though `Text.module.css` already implements `data-size` and `data-weight` rules.
- **Responsive typography utility vs. primitives**: `@primer/css` utilities (`.f00`–`.f6`) step up at `md` (e.g., `f1` 26px mobile -> 32px desktop), whereas `primitives/typography.json5` defines fixed `rem` tokens and notes in `$description` to swap `display` down to `title-large` on narrow viewports manually.
- **Missing typography `letter-spacing` rule on live site**: The explicit `letter-spacing` prohibition from archived `primer/design/content/foundations/typography.mdx` is absent on the condensed live `foundations/typography` page, though still good defensive guidance.
