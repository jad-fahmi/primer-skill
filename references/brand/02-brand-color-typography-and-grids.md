# Brand Color, Typography, and Gridline Architecture

> Documents GitHub Brand's dual swatch/token palettes (`#0FBF3E` hero green, 6-step Brand Toolkit print/digital swatches vs 10-step `@primer/brand-primitives` web tokens), `Mona Sans` variable-weight physics, responsive type scale (`100`–`1000`), and the 12-column Grid / Flexsuite gridline layout system.

## Contents

- [Key takeaways](#key-takeaways)
- [Brand color philosophy and ratio](#brand-color-philosophy-and-ratio)
- [Brand Toolkit swatches (HEX, RGB, CMYK, PMS)](#brand-toolkit-swatches-hex-rgb-cmyk-pms)
- [Primer Brand web design tokens (`@primer/brand-primitives`)](#primer-brand-web-design-tokens-primerbrand-primitives)
- [Brand typography and variable font weights](#brand-typography-and-variable-font-weights)
- [Grid, spacing, border, and Gridline/Flexsuite architecture](#grid-spacing-border-and-gridlineflexsuite-architecture)
- [Off-brand and generic AI tells](#off-brand-and-generic-ai-tells)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- **[Universal]** Establish brand distinctiveness through color *proportion* (dominant neutral surfaces + one crisp hero accent), rather than bathing entire pages in saturated brand fills or neon multi-stop gradients.
- **[Universal]** Large display headlines built with variable fonts need lighter optical weights (`~425`–`480`) and tighter negative tracking than body copy so glyphs do not blot together at `48px`–`64px`.
- **[Brand]** GitHub Brand centers on **GitHub Green (`#0FBF3E`)** anchored by tinted cool-green-gray neutrals (`#F2F5F3` to `#101411` in Toolkit swatches; 10-step `0..9` scales in `@primer/brand-primitives`), reserving wider spectrum hues (blue, indigo, purple, red, pink, orange, yellow) for expressive illustrations and thematic accents.
- **[Brand]** Typography pairs **Mona Sans** (display/body), **Hubot Sans** (optional alt display voice), and **Mona Sans Mono** (eyebrows/code moments). Never apply `font-weight: 800` or `900`, uppercase multiline monospace, manual ligatures in headlines/body, or manual tracking overrides (`Mona Sans v2` applies optical sizing automatically).
- **[Brand]** Modern GitHub marketing pages follow the **Gridline / Flexsuite** aesthetic: crisp hairline section boundaries via `variant="gridline"` / `variant="gridline-expressive"`, clear separation between neutral text zones and expressive visual zones (often marked by a solid GitHub Green accent rule), generous whitespace, and a 12-column responsive grid.
- **[Brand]** Do not confuse Brand tokens (`--brand-color-*`, `--brand-text-*`) with Primer Product tokens (`--bgColor-*`, `--fgColor-*`) or import `@primer/react` and `@primer/react-brand` stylesheets interchangeably.

## Brand color philosophy and ratio

Both `brand.github.com` and `@primer/react-brand` start from the same visual rule: **neutral dominance with punctuated green**.

1. **Primary hierarchy**: Most page canvas area stays white, tinted light gray, or deep charcoal/black (`canvas.default`, `canvas.subtle`). **GitHub Green (`#0FBF3E`)** enters sparingly as primary CTAs, structural accent divider lines, focus highlights, and restrained illustration anchors.
2. **Dark vs light theme parity**: Unlike Product UI's multi-theme matrix (9+ high-contrast/colorblind themes), Brand UI uses three `ThemeProvider` `colorMode` values: `'light'`, `'dark'`, and `'auto'` (emitting `data-color-mode` on wrappers so mixed light/dark bands can alternate on a single marketing page).
3. **Neutral-backed legibility**: Text and complex expressive art occupy *separate* layout cells or receive neutral backing layers so copy stays strictly above WCAG AA (`4.5:1` normal text, `3:1` large text) without resting directly over noisy illustration or dithering.
4. **No purple/indigo AI cliché**: Even though indigo/purple appear in GitHub's Wider Spectrum illustration swatches, `@primer/brand-mcp` explicitly flags generic purple/indigo CSS gradients and glow cards as an off-brand / AI-slop tell on web pages.

Sources: `https://brand.github.com/foundations/color/`, `https://brand.github.com/graphic-elements/layouts/`, `primer/brand` (`apps/next-docs/content/introduction/theming.mdx`, `packages/mcp/src/review/rules.ts`).

## Brand Toolkit swatches (HEX, RGB, CMYK, PMS)

For print, brand collateral, and cross-media art direction, `brand.github.com` defines compact 1–6 / 1–7 named palettes with Pantone (PMS) and CMYK equivalents:

### Primary palette

| Swatch | HEX | RGB | CMYK | PMS |
| --- | --- | --- | --- | --- |
| **GitHub Green** | `#0FBF3E` | `15/191/62` | `92/0/67/25` | `360` |
| Gray 1 | `#F2F5F3` | `242/245/243` | `1/0/1/4` | `Cool Gray 1` |
| Gray 2 | `#E4EBE6` | `228/235/230` | `3/0/2/8` | `441` |
| Gray 3 | `#B6BFB8` | `182/191/184` | `5/0/4/25` | `442` |
| Gray 4 | `#909692` | `144/150/146` | `4/0/3/41` | `443` |
| Gray 5 | `#232925` | `35/41/37` | `15/0/10/84` | `447` |
| Gray 6 | `#101411` | `16/20/17` | `20/0/15/92` | `Process Black` |
| Green 1 | `#BFFFD1` | `191/255/209` | `20/0/30/0` | `7486` |
| Green 2 | `#8CF2A6` | `140/242/166` | `42/0/31/5` | `7487` |
| Green 3 | `#5FED83` | `95/237/131` | `59/0/45/7` | `2268` |
| Green 4 *(Hero)* | `#0FBF3E` | `15/191/62` | `92/0/67/25` | `360` |
| Green 5 | `#08872B` | `8/135/43` | `94/0/68/47` | `362` |
| Green 6 | `#0A241B` | `10/36/27` | `100/0/81/66` | `3435` |

### Wider spectrum illustration & accent swatches

Reserved primarily for key illustrations and thematic visual extensions—never as unfiltered page-wide UI noise:

- **Blue 1–7**: `#8DD6FF` (45/16/0/0, PMS 2905), `#5FB9FF` (63/27/0/0, 2915), `#3094FF` (81/42/0/0, 2995), `#1A61FE` (90/62/0/0, 2727), `#0527FC` (98/85/0/1, 2736), `#0040A7` (100/62/0/35, 2945), `#002F7A` (100/61/0/52, 289).
- **Indigo 1–4**: `#B3C1FD` (29/24/0/1, 2706), `#6B7BEF` (55/49/0/6, 2725), `#4956E5` (68/62/0/10, 2735), `#583AF7` (64/77/0/3, 2665).
- **Purple 1–4**: `#DBBFFD` (13/25/0/1, 2567), `#C898FD` (21/40/0/1, 2577), `#B870FF` (28/56/0/0, 2585), `#8534F3` (45/79/0/5, 2597).
- **Red 1–3**: `#FFE2F3` (0/11/5/0, 706), `#FFABA8` (0/33/34/0, 1775), `#FA4549` (0/72/71/2, 1788).
- **Yellow 1**: `#C7A60B` (0/17/94/22, 7755).
- **Orange 1–2**: `#DA7210` (0/48/93/15, 1585), `#B85B06` (0/51/97/28, 1525).
- **Pink 1–2**: `#FF80D2` (0/50/18/0, 224), `#F73678` (0/78/51/3, 213).

Sources: `https://brand.github.com/foundations/color/`.

## Primer Brand web design tokens (`@primer/brand-primitives`)

In code, `@primer/brand-primitives` extends Primer Primitives' Style Dictionary pipeline using the `brand` CSS namespace for functional tokens and unprefixed `--base-*` names for base ramps:

1. **10-step Base Color Scales (`0..9`)**:
   - Base tokens (`tokens/base/colors/{light,dark}.json`) define 10 steps across `black`, `white`, `transparent`, `gray`, `blue`, `green`, `yellow`, `orange`, `red`, `purple`, `pink`, `coral`, `lemon`, `lime`, `teal`, and `indigo` (compiled as `--base-color-scale-<hue>-<0..9>`).
   - **Important step-offset difference**: In the 10-step web ramp (`light.json`), GitHub Green `#0FBF3E` maps to `green-5` (`gray` spans `#F2F5F3` at step `0` to `#191F1B` at step `9`). In `dark.json`, `#0FBF3E` shifts to `green-4` (`green-5` is `#08872B`, `gray` runs `#D2D9D4` to `#060907`). Always bind UI CSS to **functional** `--brand-color-*` tokens so mode switching handles step inversion automatically.
2. **Functional Semantic Color Roles (`--brand-color-*`)**:
   - **Canvas surfaces**: `canvas.{default, invert, overlay, inset, subtle, muted}` (plus component variables such as `--brand-gridline-*` / `--brand-section-*`).
   - **Typography & links**: `text.{default, muted, subtle, onEmphasis, emphasized, link.rest, link.pressed, danger, error}`.
   - **Accents & neutrals**: `accent.{primary, secondary}`, `neutral.{emphasisPlus, emphasis, muted, subtle}`.
   - **Borders & focus**: `border.{default, muted, subtle}`, `focus`.
   - **Status**: `success.{fg, emphasis, muted, subtle}`, `danger.{fg, emphasis, muted, subtle}`, `error.{fg, emphasis, muted, subtle}`.
3. **Never hardcode raw hex or RGB** in application CSS inside `@primer/react-brand` projects (`@primer/brand-mcp` fails reviews containing hex literals when semantic `--brand-*` tokens exist).

Sources: `primer/brand` (`packages/design-tokens/scripts/build-tokens.js`, `packages/design-tokens/src/tokens/base/colors/{light,dark}.json`, `packages/design-tokens/src/tokens/functional/colors/global.json`, `packages/mcp/src/review/rules.ts`).

## Brand typography and variable font weights

GitHub Brand relies on the open-source **Mona Sans** superfamily (v2.0+ variable font featuring optical size `opsz`, 5 widths, italics, and **Mona Sans Mono**), paired optionally with **Hubot Sans** (`fontFamilyAlt`) in `@primer/react-brand`:

- **Optical size vs manual tracking**: Mona Sans v2.0+ automatically adjusts letterspacing via its `opsz` axis. On editorial/brand assets, designers should avoid stacking arbitrary manual letterspacing on top of `Mona Sans`, disable distracting ligatures in headings and body copy, reserve narrow/wide variable width axes strictly for special events (e.g., Universe), and avoid multiline ALL-CAPS monospace text (poor block legibility).
- **Calibrated variable weight tokens (why `800`/`900` is banned)**: Unlike static system-UI fonts that jump `400 / 600 / 700`, `@primer/brand-primitives` tunes exact variable axes per role and viewport:
  - **Text weights (`--brand-text-weight-*`)**: `normal: 400`, `medium: 500`, `semibold: 525`, `bold: 537`, `extrabold: 543`, `heavy: 550`. Note that even `"heavy"` body text stops at axis `550`!
  - **Heading weights (`--brand-heading-weight-*`)**: Display scales invert weight upward vs size—the largest display scale (`Heading 1000` / `--brand-text-size-1000`) steps *down* from `480` on small screens to `425` at `>= 1012px` with `-0.035em` letter spacing (`Heading.module.css` + `typography-responsive.json`), keeping giant headlines crisp rather than bloated.
  - Therefore, `@primer/brand-mcp` flags `font-weight: 800` or `900`, `serif` fallback hacks, and generic uncalibrated display weights as off-brand defects.
- **Responsive Type Scale (`--brand-text-size-*`, 100–1000)**:
  - Fixed small/body steps: `100` (`14px`), `200` (`16px` default body), `300` (`18px`).
  - Responsive fluidly stepped headings/displays across breakpoints (`narrow -> medium 768px -> large 1012px`):
    - `350`: `18px -> 20px`
    - `400`: `20px -> 22px`
    - `500`: `22px -> 24px -> 28px`
    - `600`: `24px -> 28px -> 34px`
    - `700`: `28px -> 34px -> 40px`
    - `800`: `32px -> 40px -> 48px`
    - `900`: `36px -> 48px -> 56px`
    - `1000` (`display`): `40px -> 56px -> 64px`
  - Plus `subhead.medium` and `subhead.large` semantic presets (exposed via `<Heading size="subhead-medium" | "subhead-large" | "display" | "1".."6">`).
- **Text wrap & smoothing**: `Heading` defaults cleanly with `text-wrap: balance` options, and dark mode applies `-webkit-font-smoothing: antialiased` on both `Heading` and `Text` to prevent dark-background bloom.

Sources: `https://brand.github.com/foundations/typography/`, `primer/brand` (`packages/design-tokens/src/tokens/functional/typography/typography-responsive.json`, `packages/react/src/Heading/Heading.module.css`, `packages/react/src/Text/Text.module.css`, `packages/mcp/src/review/rules.ts`).

## Grid, spacing, border, and Gridline/Flexsuite architecture

Brand layouts balance strict architectural containment with airy breathing room:

### Breakpoints, radii, borders, and base spacing

| Token group | Verified Brand primitive values |
| --- | --- |
| Breakpoints (`--brand-breakpoint-*`) | `xsmall: 320px`, `small: 544px`, `medium: 768px`, `large: 1012px`, `xlarge: 1280px`, `xxlarge: 1400px` (`narrow < 768`, `regular >= 768`, `wide >= 1280/1400` in component responsive maps) |
| Base size scale (`--base-size-*`) | `2, 4, 6, 8, 12, 16, 20, 24, 28, 32, 36, 40, 44, 48, 60, 64, 80, 88, 96, 112, 128` (`px` compiled to `rem` where configured) |
| Border widths | `thin: max(1px, 0.0625rem)`, `thick: 2px`, `thicker: 4px` |
| Border radii | `small: 4px`, `medium: 8px`, `large: 16px`, `xlarge: 24px`, `full: 9999px` |
| Control targets & sizes | Minimum target `fine: 16px`, `coarse: 44px`; Button/control heights `small: 32px`, `medium: 43px`, `large: 60px`; interaction transition `80ms` |
| Section / Stack padding (`condensed` / `normal` / `spacious`) | `condensed: 16px`, `normal: 24px`, `spacious: 48px` (Section vertical padding steps further across viewports) |
| 12-column `Grid` | Gutters `16px` (`<768px`) `-> 32px` (`>=768px`) `-> 48px` (`>=1280px`); outer margins `16px -> 40px -> 16px` |

### Gridline & Flexsuite page system

GitHub's modern marketing web aesthetic (`page-design.md`, `brand.github.com/graphic-elements/layouts/`) relies on **Gridline** variants and **Flexsuite** composition:

- **Visible vs invisible grid**: Divide major structural cells cleanly with 1px structural borders (`variant="gridline"` / `variant="gridline-expressive"` on `Hero`, `Section`, `River`, `RiverAccordion`, `RiverBreakout`, `SectionIntroStacked`, `LogoSuite`, `FAQ`, and `variant="gridlines"` on `Tiles`), then reveal white space invisibly inside sub-blocks so borders never turn busy or wireframe-like.
- **Text / visual segregation**: Place copy in clean neutral cells and visual assets in adjacent cells, separated at major narrative seams by borders or a crisp **GitHub Green** rule (`#0FBF3E`).
- **Grid vs Stack**: Use `<Grid>` (`12` columns) for explicit horizontal multi-column alignment across breakpoints (`Hero`, multi-card rows, Flexsuite bands); use `<Stack>` for simple vertical or horizontal spacing of sibling items. Unlike Product UI (`@primer/react`), **Brand `<Box>` is explicitly supported and idiomatic** for layout wrappers, spacing, and token-bound borders/backgrounds (`backgroundColor="default" | "subtle"`).
- **Photography & speakers**: When photography appears alongside graphic illustration, desaturate/grayscale speaker or portrait photos if color would compete with illustration focal art.

Sources: `https://brand.github.com/graphic-elements/layouts/`, `primer/brand` (`packages/design-tokens/src/tokens/functional/size/*.json`, `packages/design-tokens/src/tokens/functional/components/grid/grid.json`, `packages/mcp/content/page-design.md`, `packages/react/src/Section/Section.tsx`).

## Off-brand and generic AI tells

`@primer/brand-mcp`'s automated review rules (`rules.ts`) encode concrete anti-patterns that expose unreviewed AI-generated pages or drift from modern GitHub Brand design:

1. **Cliché AI gradients & glass**: Generic purple/indigo CSS gradients, deprecated GitHub box-shadow glow blobs around dark cards, or custom `backdrop-filter: blur(...)` hacks on random panels (note: `@primer/react-brand` exports an explicit `FrostedGlassVFX` utility ONLY for intentional controlled overlays—do not sprinkle frosted glass across generic cards).
2. **Wrong pill / heavy display habits**: Turning primary CTAs into rounded-full pill buttons (unless using a scoped pill tag/`Token`) or using `font-weight: 800`/`900` and serif fonts.
3. **Raw unstyled screenshots**: Dropping bare `<img>` UI captures directly onto the page canvas instead of wrapping them in the brand backdrop/dither + matched-border treatment.
4. **Hardcoded palette literals**: Writing inline hex codes (`#0FBF3E`, `#ffffff`) or uncalibrated spacing numbers (`17px`, `35px`) where `--brand-*` / `Stack` / `Grid` tokens apply.
5. **Zig-zagging gridline rivers**: Alternating image-left / image-right on `gridline` rivers breaks the vertical spine rule (`River` must stay `align="start"` inside gridline sections).

Sources: `primer/brand` (`packages/mcp/src/review/rules.ts`, `packages/mcp/content/page-design.md`, `apps/next-docs/content/components/River/index.mdx`).

## Agent application notes

- **Non-GitHub marketing websites**: Adopt the *system physics*—one primary hero color + tinted neutral surface ramp (not plain `#FFFFFF`/`#F5F5F5`), optical weight compensation (`425`–`550` variable range on large display fonts so giant headlines look sharp), 12-column Grid with explicit mobile/tablet/desktop gutter progression, clean text-over-neutral legibility separation, and banning purple glow/backdrop-blur AI clichés. Swap GitHub Green and Mona Sans out for the target brand's actual tokens and typography.
- **Official `@primer/react-brand` pages**: Always check dark/light contrast with `ThemeProvider` (`colorMode="auto" | "dark" | "light"`), bind custom CSS exclusively to `--brand-*` variables (importing `@primer/react-brand/lib/css/main.css`), keep `Heading` weights controlled via props (`weight` / `size`), and validate gridline sections (`align="start"`, non-alternating `River`).

## Gaps, caveats and source issues

- **Print vs Web swatch index offset**: `brand.github.com` tables number Primary `Gray 1..6` and `Green 1..6` (where Hero GitHub Green is `Green 4`), whereas `@primer/brand-primitives` exposes 10-step `0..9` ramps (`--base-color-scale-green-5` in light mode, `--base-color-scale-green-4` in dark mode). Mixing swatch names with CSS variable indices without translation causes bugs; always check mode-aware functional `--brand-*` tokens in code.
- **Unindexed print typography spreads**: `brand.github.com/foundations/typography/` renders illustrative type-scale graphics whose individual print point/leading tables reside inside outlined plates in `brand-guidelines-2026.pdf`; all exact web font sizes (`14px`–`64px`) and weights cited here come directly from verified `@primer/brand-primitives` JSON/CSS source files.
- **`Tiles` variant prop spelling quirk**: Most Gridline components accept `variant="gridline"` (singular), whereas `Tiles.tsx` uses `"gridlines"` (plural). Check component TypeScript definitions before passing `variant`.
