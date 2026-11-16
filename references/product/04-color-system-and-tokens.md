# Primer Product Color System, Semantics, Contrast, and Design Tokens

> Consolidates Primer's semantic color architecture, 14-step neutral scale mechanics, theme/mode system, WCAG color contrast enforcement, token naming grammar, and consumption/migration rules.

## Contents

- [Key takeaways](#key-takeaways)
- [Three-tier token architecture and naming grammar](#three-tier-token-architecture-and-naming-grammar)
- [Neutral color architecture and contrast zones](#neutral-color-architecture-and-contrast-zones)
- [Semantic color roles and pairing rules](#semantic-color-roles-and-pairing-rules)
- [Themes, color modes, and high-contrast variants](#themes-color-modes-and-high-contrast-variants)
- [Color accessibility and component contrast rules](#color-accessibility-and-component-contrast-rules)
- [Consuming and migrating tokens in CSS and React](#consuming-and-migrating-tokens-in-css-and-react)
- [Architectural rationale from Primitives ADRs](#architectural-rationale-from-primitives-adrs)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Universal]` **Never reference raw hex colors or non-semantic base scales directly in UI code**: bind UI elements to **functional semantic tokens** (`fgColor`, `bgColor`, `borderColor`) so light, dark, high-contrast, and colorblind themes adapt automatically with guaranteed contrast.
- `[Universal]` **Structure neutral scales in three non-linear regions**: steps `0–6` for backgrounds, `7–8` for borders/dividers, and `9–13` for readable text/icons (with an intentional lightness jump between steps `7` and `8`). Inverting the neutral direction between light and dark lets functional tokens share structure.
- `[Primer]` **Enforce exact foreground/background pairing rules**: pair `--bgColor-*-emphasis` only with `--fgColor-onEmphasis`, pair `--bgColor-*-muted` with `--fgColor-{semantic}` (or `--fgColor-default`), and **never** pair `--bgColor-muted` with `--fgColor-muted` (fails contrast guarantees).
- `[Universal]` **Never convey state or meaning by color alone**: pair semantic colors with clear wording, distinct icon shapes, patterns, or leader lines—and distinguish **text contrast (4.5:1)** from **non-text input boundaries / focus rings / functional icons (3:1)**.
- `[Universal]` **Design tokens belong to the product**: in non-GitHub products, adopt Primer's 3-tier token architecture and pairing rules while defining your own neutral/brand palette rather than importing GitHub's blue/green defaults.

## Three-tier token architecture and naming grammar

Primer (`@primer/primitives`) aligns source files with the **W3C Design Tokens format** (`$value`, `$type`, `$description`, optional `alpha`, `$deprecated`, and `$extensions` such as `org.primer.overrides`) compiled via Style Dictionary.

### The three token tiers `[Universal]` / `[Primer]`

| Tier | Role | Rule | Examples |
| --- | --- | --- | --- |
| **1. Base tokens** | Raw palette/scale primitives (`0–13` hues, raw sizes, durations) | **Never use directly in product UI or CSS.** Only referenced by Functional or Component tokens; do not adapt across themes. | `base-size-4`, `base-color-green-5`, `brand-base-color-lime-5` |
| **2. Functional tokens** | Global semantic UI intent (text, background, border, shadow, stack, text styles) | **Default choice for 95%+ of UI styling.** Theme-aware and contrast-checked. | `--fgColor-default`, `--bgColor-muted`, `--borderColor-danger-emphasis`, `--focus-outlineColor` |
| **3. Component / Pattern tokens** | Component-specific states (`button-*`, `control-*`, `overlay-*`, `avatar-*`) | **Use for interactive controls and specialized surfaces** (`button` for buttons, `control` for form inputs/checkboxes/interactive items). Avoid over-proliferating per-component tokens when a functional token works. | `--button-primary-bgColor-hover`, `--control-danger-borderColor-rest`, `--overlay-bgColor` |

### Token naming grammar (`ADR-006`, `token-names`) `[Primer]`

Full pattern: `[prefix]-[namespace]-[pattern]-[variant*]-[property]-[variant*]-[scale]` (CSS uses `-` delimiters; JS uses `.`; multi-word segments use `camelCase`; required block is `property`).

- **Prefix** (optional): encapsulates flavors (`brand-` for Primer Brand marketing tokens; product `@primer/primitives` intentionally omits a `primer-` prefix per `ADR-005` to avoid huge migration churn).
- **Namespace**: `base` for lowest-level raw constants; omitted on functional/component tokens.
- **Pattern**: cross-component concept (`control`, `overlay`, `text`, `stack`) or component (`button`, `treeView`).
- **Property** (required): CSS or conceptual property (`fgColor`, `bgColor`, `borderColor`, `paddingInline`, `minTarget`).
- **Variant** (at most one modifying pattern or property): color role (`danger`, `accent`) or modifier (`default`, `muted`, `emphasis`, where `subtle` was deprecated in favor of `muted` per `ADR-009`).
- **Scale**: t-shirt sizes (`xsmall..xxlarge`), density (`condensed | normal | spacious`, default `normal`), thickness (`thin | thick | thicker`, default `thin`), viewport ranges (`narrow | regular | wide`), or interaction states (`rest | hover | active | selected | disabled`).

Sources: [https://primer.style/product/primitives/token-names/](https://primer.style/product/primitives/token-names/); `primer/primitives` `contributor-docs/adrs/adr-001`..`011.md`, `DESIGN_TOKENS_GUIDE.md`.

## Neutral color architecture and contrast zones

Primer's neutral scales use **14 steps (`0–13`)** in both light and dark modes, with inverted polarity (light starts at white `#ffffff` at step 0; dark starts near black at step 0) so functional tokens map consistently across themes:

| Step Range | Light Mode Job | Lightness Pattern (`color-scales.md`) | Contrast Target / Rule |
| --- | --- | --- | --- |
| **Steps `0–6`** | Backgrounds (`bgColor-default`, `bgColor-muted`, `bgColor-inset`, control rest/hover/active states) | Gentle ~2–3% lightness drops per step (`100 -> 82%`) | Contrast of all text/borders is calculated against `bgColor-muted` (not only pure white `default`) so text stays readable on subtle surfaces too. |
| **Steps `7–8`** | Borders & dividers (`borderColor-muted`, `borderColor-default`) | Intentional **7 -> 8 transition break** (lightness drops sharply ~27% into step 8 at ~55%) | **Step 8** is the minimum 3:1 border contrast against `bgColor-muted` for interactive controls. |
| **Steps `9–13`** | Text, icons, highest-contrast foregrounds (`fgColor-muted`, `fgColor-default`) | ~8% lightness drops per step (`55 -> 14%`) | Step 9 passes WCAG text contrast against steps 0–4; step 10 passes against steps 5–6. |

*Note on High Contrast scaling*: `light_high_contrast` shifts further along the scale—steps `2–7` for backgrounds, step `10` for borders, and `11–13` for text—targeting **7:1 minimum** for most text and interactive UI.

*Scale maintenance insight (`color-scales.md`)*: Never arbitrarily shift the job of steps `0–13` when tweaking brand tint (e.g., shifting blue-gray to green-gray), because functional tokens anchor to those step jobs (~1,800+ references); add extension steps (`14+`) if finer background granularity is needed.

Sources: [https://primer.style/product/getting-started/foundations/color-usage/](https://primer.style/product/getting-started/foundations/color-usage/); `primer/primitives` `contributor-docs/design/color-scales.md`, `adrs/adr-003-neutral-scales.md`.

## Semantic color roles and pairing rules

### Complete semantic role inventory `[Primer]`

Live `color-usage` summarizes 8 primary roles; inspecting `@primer/primitives` (`fgColor.json5`, `bgColor.json5`, `borderColor.json5`) shows **12 total semantic roles** across product UI (light-theme sample values verified in `product/primitives/color`):

| Semantic Role | Meaning / Intended Usage | Sample `--fgColor-*` (Light) | Sample `--bgColor-*-emphasis` / `-muted` (Light) |
| --- | --- | --- | --- |
| `neutral` | Neutral status tags, subdued badges | `#59636e` | `#59636e` / `#818b981f` |
| `accent` | Links, selected/active/focus states, neutral informational callouts | `#0969da` | `#0969da` / `#ddf4ff` |
| `success` | Primary CTA buttons, positive/passing feedback | `#1a7f37` | `#1f883d` / `#dafbe1` |
| `open` | Open issues, pull requests, active workflows (GitHub-specific) | `#1a7f37` | `#1f883d` / `#dafbe1` |
| `attention` | Warnings, queued PRs, active/in-progress checks | `#9a6700` | `#9a6700` / `#fff8c5` |
| `severe` | Critical warnings higher than attention, short of destructive error | `#bc4c00` | `#bc4c00` / `#fff1e5` |
| `danger` | Destructive buttons, blocking errors, failures | `#d1242f` | `#cf222e` / `#ffebe9` |
| `closed` | Closed/unmerged issues or PRs (GitHub-specific) | `#d1242f` | `#cf222e` / `#ffebe9` |
| `draft` | Draft items / non-active states | `#59636e` | `#59636e` / `#818b981f` |
| `done` | Completed/merged PRs and resolved workflows | `#8250df` | `#8250df` / `#fbefff` |
| `upsell` | Plan upgrades and promotional product prompts | `#8250df` | `#8250df` / `#fbefff` |
| `sponsors` | GitHub Sponsors UI (GitHub-specific) | `#bf3989` | `#bf3989` / `#ffeff7` |

**Categorical / Data palettes (`@primer/primitives`)**:
- `--display-*-*` (**19 named colors**: `blue`, `green`, `orange`, `purple`, `plum`, `red`, `coral`, `yellow`, `gray`, `auburn`, `brown`, `lemon`, `olive`, `lime`, `pine`, `teal`, `cyan`, `indigo`, `pink`) for non-semantic categorization (user labels, language tags, avatar backgrounds).
- `--data-*-color-emphasis` / `--data-*-color-muted` (**17 named colors** excluding `cyan` and `indigo`) for data visualizations (`data-vis.json5`).

### Mandatory pairing logic matrix (`DESIGN_TOKENS_GUIDE.md` & `@primer/mcp`) `[Primer]` / `[Universal]`

| Background Surface | Required Text / Icon Foreground | Border Pairing & Rules |
| --- | --- | --- |
| `--bgColor-default` (`#ffffff` light) | `--fgColor-default` or `--fgColor-muted` | `--borderColor-default` / `--borderColor-muted` |
| `--bgColor-muted` (`#f6f8fa` light) | `--fgColor-default` (**NEVER `--fgColor-muted`**) | `--borderColor-default` (muted text on muted background risks failing 4.5:1) |
| `--bgColor-inset` | `--fgColor-default` or `--fgColor-muted` (verify 4.5:1) | Subtle recessed containers; avoid stacking borders unnecessarily |
| `--bgColor-{role}-muted` | `--fgColor-{role}` or `--fgColor-default` | Pair with `--borderColor-{role}-muted` for alert banners, callouts, and tags |
| `--bgColor-{role}-emphasis` | `--fgColor-onEmphasis` (**MUST pair**) | White (`#ffffff`) across **all** light and dark themes (`ADR-002`), including `dark-high-contrast` where darker backgrounds + higher-contrast borders compensate |
| `--bgColor-disabled` | `--fgColor-disabled` | **Never** simulate disabled states with `opacity < 1` (`DESIGN_TOKENS_GUIDE.md`) |

Sources: [https://primer.style/product/getting-started/foundations/color-usage/](https://primer.style/product/getting-started/foundations/color-usage/); [https://primer.style/product/primitives/color/](https://primer.style/product/primitives/color/); `primer/primitives` `src/tokens/functional/color/*.json5`, `DESIGN_TOKENS_GUIDE.md`, `adrs/adr-002-fgColor-onEmphasis.md`.

## Themes, color modes, and high-contrast variants

### Documented vs. built themes `[Primer]`

The live `product/primitives` documentation exposes **9 user-selectable themes** (`light`, `light_tritanopia`, `light_high_contrast`, `light_colorblind`, `dark`, `dark_colorblind`, `dark_dimmed`, `dark_high_contrast`, `dark_tritanopia`), while `primer/primitives/scripts/themes.config.ts` actually builds **14 CSS files** (adding `light-colorblind-high-contrast`, `light-tritanopia-high-contrast`, `dark-dimmed-high-contrast`, `dark-colorblind-high-contrast`, and `dark-tritanopia-high-contrast`).

### How theme switching works `[Primer]`

- **CSS / Primitives (`data-*` attributes on `<body>` or container)**:
  - `data-color-mode`: `"auto" | "light" | "dark"` (`auto` tracks OS `prefers-color-scheme`).
  - `data-light-theme` / `data-dark-theme`: underscores (`light`, `dark_dimmed`, `dark_high_contrast`, etc.) matching imported CSS from `@primer/primitives/dist/css/functional/themes/*.css`.
- **Primer React (`ThemeProvider`)**:
  - Uses `colorMode="day" | "night" | "auto"` (note: `"day"`/`"night"` in React `ThemeProvider` vs `"light"`/`"dark"` in CSS `data-color-mode` and Brand `ThemeProvider`!), plus `dayScheme` and `nightScheme`.
  - Pass `preventSSRMismatch` when using `colorMode="auto"` with SSR so server and client hydration don't flash mismatched themes.
  - For custom scheme-aware colors outside standard tokens, use `useColorSchemeVar`.

Sources: [https://primer.style/product/primitives/](https://primer.style/product/primitives/); [https://primer.style/product/getting-started/react/theming/](https://primer.style/product/getting-started/react/theming/); `primer/primitives` `scripts/themes.config.ts`.

## Color accessibility and component contrast rules

GitHub mandates **WCAG 2.2 Level AA** (and Microsoft Accessibility Standards `MAS-C`). Note that **APCA** (proposed for WCAG 3.0) is **explicitly not normative**—do not accept an APCA pass that fails WCAG 2.2 ratios.

### Contrast requirements by UI element (`color-considerations`, `ADR-010`) `[Universal]`

| Element / Pair | Minimum Ratio | Rule & Nuance |
| --- | --- | --- |
| **Normal text & input values/labels vs. bg** | **4.5:1** | Applies to text, placeholder text (unless a visible persistent `<label>` exists, though visible labels are required anyway), and text-only validation messages. |
| **Large text vs. bg** | **3:1** | Per WCAG SC 1.4.3 (`>= 18pt` / `24px`, or `>= 14pt` / `18.67px` bold). |
| **Links vs. surrounding text** | **3:1** (or underlined) | Links must have **both** `4.5:1` against background **and** `3:1` against adjacent non-link text if not underlined. |
| **Input borders / borderless input fills vs. canvas** | **3:1** | Users must perceive interactive input boundaries; border vs input's own fill is not restricted, only boundary vs surrounding background. |
| **Checkbox / radio selected indicators** | **3:1** | Checkmarks/radio dots and adjacent state comparisons (e.g., active vs inactive segment in `SegmentedControl`) need `3:1`. |
| **Focus indicator (`:focus-visible`) vs. adjacent colors** | **3:1** | 1–2px focus ring (`--focus-outline`) must maintain `3:1` against adjacent background/border. |
| **Button text vs. button bg** | **4.5:1** (3:1 large) | Button outer border contrast is *not* normatively required when button text/icon meets contrast, though clear boundaries aid usability. |
| **Functional (icon-only) button icon vs. bg** | **3:1** | `IconButton` icons convey meaning alone and require `3:1`. Icons paired with visible text labels are decorative (still recommended `3:1`). |
| **Disabled controls & purely decorative elements** | Exempt | Normatively exempt from contrast ratios, **but** Primer warns against disabling submit buttons before validation and recommends keeping disabled UI discoverable. |
| **Hover vs. rest state delta** | Exempt | No minimum contrast delta between hover and rest on the same element. |

### Non-default themes & OS display modes `[Universal]`

- **Default Day & Night themes must pass 100% WCAG AA** because logged-out users receive them automatically.
- **Intentionally lower-contrast sub-themes** (`dark_dimmed`) are permitted under **WCAG Technique G174** (providing a compliant control to switch to high-contrast/default themes) to accommodate users with photophobia or contrast sensitivity.
- **Respect `forced-colors` mode**: Never override Windows/OS `forced-colors` dynamic system colors with custom aesthetic hacks; limit adjustments to preserving semantic legibility. Note that GitHub does *not* auto-enable its own High Contrast themes when `prefers-contrast` is set.
- **Never rely on color alone**: Pair colors with text labels, distinct icon shapes (e.g., check vs X vs alert triangle), patterns, or chart leader lines.

Sources: [https://primer.style/accessibility/design-guidance/color-considerations/](https://primer.style/accessibility/design-guidance/color-considerations/); `primer/primitives` `contributor-docs/adrs/adr-010-color-contrast.md`, `contributor-docs/design/color-scales.md`.

## Consuming and migrating tokens in CSS and React

- **Preferred consumption (`@primer/react` + `@primer/primitives`) `[Primer]`**:
  - Import theme CSS (`@primer/primitives/dist/css/functional/themes/light.css`, `dark.css`, etc.) and reference CSS variables directly in CSS Modules (`color: var(--fgColor-default);`, `background-color: var(--bgColor-muted);`).
  - Avoid raw hex/px values, avoid deprecated `Box` / `sx` props, and avoid legacy JS `theme` object references in new code (which internally wrap CSS vars).
  - Validate CSS with `stylelint` / `@primer/stylelint-config` (or `@primer/mcp` `lint_css`).
- **Migrating legacy `sx` & SCSS tokens (`product/primitives/migrating/`) `[Primer]`**:
  - `canvas.default` -> `--bgColor-default`; `canvas.subtle` -> `--bgColor-muted`; `canvas.inset` -> `--bgColor-inset`; `canvas.overlay` -> `--overlay-bgColor`.
  - `fg.default` -> `--fgColor-default`; `fg.muted` / eliminated `fg.subtle` -> `--fgColor-muted`; `fg.onEmphasis` -> `--fgColor-onEmphasis`.
  - `{role}.fg` -> `--fgColor-{role}`; `{role}.emphasis` -> `--bgColor-{role}-emphasis`; `{role}.muted` -> `--borderColor-{role}-muted`; `{role}.subtle` -> `--bgColor-{role}-muted`.
  - Margin tokens were intentionally omitted from new functional spacing primitives (`migrating.md`): replace outer margins with container padding and flex/grid `gap`.

Sources: [https://primer.style/product/primitives/](https://primer.style/product/primitives/); [https://primer.style/product/primitives/migrating/](https://primer.style/product/primitives/migrating/); `primer/react` `packages/mcp/src/server.ts`.

## Architectural rationale from Primitives ADRs

1. **`ADR-001` (`default` aliases)**: Always alias `default` to an explicit step (`medium`, `thin`) so consumers have a safe no-guesswork choice without breaking scale consistency.
2. **`ADR-002` (`fgColor-onEmphasis` uniformity)**: Standardize `--fgColor-onEmphasis` as white across all dark/light themes (darkening emphasis fills and adding borders in `dark-high-contrast`) so component contrast logic stays predictable.
3. **`ADR-004` & `ADR-008` & `ADR-011` (Source format)**: Author tokens in W3C JSON5 with `hex6` + explicit `alpha` (instead of `hex8`) and theme `$extensions` (`org.primer.overrides`) so one component file handles all themes cleanly and Style Dictionary compiles CSS/JS/Figma targets.
4. **`ADR-009` (`muted` replaces `subtle`)**: Consolidate three semantic variants (`default`, `muted`, `emphasis`) to minimize cognitive load and token sprawl.

Sources: `primer/primitives` `contributor-docs/adrs/adr-001`..`011.md`.

## Agent application notes

- **Building a custom token layer for non-GitHub apps `[Inferred]`**: Replicate Primer's **architecture**, not its colors:
  1. Define a non-linear neutral scale (`0–6` surfaces, `7–8` 3:1 borders, `9–13` >= 4.5:1 text) + brand accent + semantic status roles (`accent`, `success`, `warning`, `danger`, `neutral`) with `muted` (tinted surface + semantic text) and `emphasis` (solid fill + guaranteed `onEmphasis` text).
  2. Expose only **functional CSS custom properties** to components (`--bg-default`, `--bg-muted`, `--fg-default`, `--border-default`), keeping raw hex scales private.
  3. Invert neutral direction or remap functional pointers in `[data-theme="dark"]` / `prefers-color-scheme: dark` and verify automated WCAG AA contrast pairs (`4.5:1` text, `3:1` input boundaries/focus rings).
- **Avoid GitHub-specific role names in generic apps `[Inferred]`**: Don't ship `--fgColor-sponsors` or `--fgColor-open` / `--fgColor-done` in an unrelated SaaS/e-commerce codebase unless the domain actually uses open/closed/done workflows.

## Gaps, caveats and source issues

- **Theme count discrepancy across docs**: `color-scales.md` refers to 8 color modes, `live/product__primitives.md` documents 9 themes, and `themes.config.ts` compiles 14 theme CSS files (including high-contrast colorblind/tritanopia/dimmed variants not listed in the docs).
- **Semantic role list differs slightly across pages**: `color-usage.md` lists 8 roles in its overview table (`accent`, `success`, `attention`, `danger`, `open`, `closed`, `done`, `sponsors`), while `primitives` source files (`fgColor.json5` / `bgColor.json5` / `borderColor.json5`) and `product/primitives/color` also include `neutral`, `severe`, `draft`, and `upsell`.
- **Token syntax transition (`v7` vs `v8+`)**: Older examples across some blog/legacy pages still show `fg.default` or `--color-fg-default` (`primer/primitives` pre-v8); current `@primer/primitives` uses `--fgColor-default`, `--bgColor-default`, `--borderColor-default`.
- **Internal coaching links**: Some contrast policy footnotes link to `github/accessibility/blob/main/docs/coaching-recommendations/...`, which are inaccessible outside GitHub.
