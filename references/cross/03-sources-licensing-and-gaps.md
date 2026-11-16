# Sources, Licensing, Trademarks, and Documentation Gaps

> Provides an auditable ledger of every primary web page and GitHub repository examined while building `primer-skill`, licensing and trademark boundaries (MIT vs SIL OFL 1.1 vs proprietary GitHub marks), and every archived, deprecated, staff-only, broken, or conflicting source encountered.

## Contents

- [Key takeaways](#key-takeaways)
- [Websites and local primary archives verified](#websites-and-local-primary-archives-verified)
- [Official GitHub repositories examined](#official-github-repositories-examined)
- [Licensing and trademark compliance matrix](#licensing-and-trademark-compliance-matrix)
- [Archived, deprecated, and superseded surfaces](#archived-deprecated-and-superseded-surfaces)
- [Private, staff-only, and inaccessible sources](#private-staff-only-and-inaccessible-sources)
- [Source discrepancies and documentation bugs observed](#source-discrepancies-and-documentation-bugs-observed)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- **[Universal]** Design knowledge, architectural rationale, and UX patterns extracted into original synthesis belong to craft knowledge; proprietary logos, mascots, downloadable brand kits, and trademarked brand names remain strictly protected intellectual property.
- **[Primer]** Every Primer software/documentation repository cloned (`primer/react`, `primer/brand`, `primer/primitives`, `primer/octicons`, `primer/css`, `primer/view_components`, `primer/behaviors`, `primer/live-region-element`, `primer/primer-style`) carries an **MIT License** (`primer/design` also contains an MIT-style permission notice from GitHub, Inc., and was archived `2025-07-02`).
- **[Brand]** The **Mona Sans** typeface (`github/mona-sans`) is licensed under the **SIL Open Font License v1.1** with Reserved Font Name `"Mona Sans"` / `"Mona"`, allowing free web/app embedding and redistribution provided font software is not sold standalone or renamed using the reserved name.
- **[Brand]** `GITHUB®`, `INVERTOCAT`, `OCTOCAT®`, `Mona`, `Ducky`, and `GitHub-Brand-Guidelines-2026.pdf` / `brand.github.com` creative assets are copyright/trademark of **GitHub, Inc. (`© 2026 GitHub, Inc. All rights reserved.`)**.

## Websites and local primary archives verified

All conclusions in `primer-skill` trace directly to crawled snapshots and official repositories inspected in this session (snapshot window: **November 2026**):

| Source Portal | Scope Verified | Notes |
| --- | --- | --- |
| `https://primer.style/` | **415 live pages** across `/product/` (`346`), `/accessibility/` (`62`), `/octicons/` (`4`), `/brand/` (`1` router/docs entry backed by `primer/brand`), `/about/` (`1`), and `/` home (`1`). | `/native/` redirects back to `/`; CLI/Desktop/Mobile native specs were preserved only in archived `primer/design` (`content/native/*.mdx`). |
| `https://brand.github.com/` | **32 live HTML/Markdown pages** across Guides, Brand Identity, Foundations, Graphic Elements, Brand in Action, and Motion Identity, plus downloadable `GitHub-Brand-Guidelines-2026.pdf` (**89 pages**). | PDF body typography is vector-outlined (`~1,680` extractable TOC/header characters); full textual rules were verified cleanly from the 32 live HTML pages and `@primer/react-brand`. |
| Supporting official essays & statements | **The Zen of GitHub** (`essay_zen.md`), **Taste** / Primer philosophy (`essay_taste.md`), and `https://accessibility.github.com/`. | Grounded product values, engineering philosophy, and accessibility conformance commitments. |

## Official GitHub repositories examined

| Repository | Purpose in Synthesis | Status Observed |
| --- | --- | --- |
| `primer/react` | `@primer/react` components, CSS Modules, `Box`/`sx` deprecations, ARIA/keyboard patterns, `@primer/mcp` guidance | Active |
| `primer/brand` | `@primer/react-brand` (55+ components, 9 forms), `@primer/brand-primitives` JSON/CSS tokens, `@primer/brand-mcp` page-design templates & review linter (`rules.ts`), `apps/next-docs` MDX docs | Active |
| `primer/primitives` | W3C JSON5 tokens, Style Dictionary build, ADRs `000–011`, `color-scales.md`, `DESIGN_TOKENS_GUIDE.md` | Active |
| `primer/octicons` | `12px`/`16px`/`24px` optical SVG construction specs, stroke/corner rules, add-icon checklist | Active |
| `primer/css` | Legacy utility classes, responsive typography/grid utilities, animation helpers | Legacy / Reference |
| `primer/view_components` | Rails ViewComponent status and parity notes | **Maintenance mode** |
| `primer/behaviors` | `focusTrap`, `focusZone`, `getAnchoredPosition` behavior primitives | Active |
| `primer/live-region-element` | Custom `<live-region>` announcement element spec and delay/politeness rules | Active |
| `primer/design` | Legacy UX patterns, Native (CLI/Desktop/Mobile), Octovisuals notes, old component lifecycle docs | **Archived `2025-07-02`** |
| `primer/primer-style` | Legacy hub docs (`illustration-guidelines.md`, site architecture) | Superseded by `primer/design` -> `primer.style` |
| `github/mona-sans` | Font licensing (`OFL.txt` / `LICENSE`) and variable-axis font architecture | Active |

## Licensing and trademark compliance matrix

`primer-skill` summarizes design concepts in original prose, avoids reproducing copyrighted artwork/photography/long verbatim documentation dumps, and separates open-source code from trademarked visual identity:

| Asset / Artifact | License / Owner | Permitted Reuse in Generated Projects | Prohibited Action |
| --- | --- | --- | --- |
| Primer design system code, `@primer/react`, `@primer/react-brand`, `@primer/primitives`, `@primer/octicons`, `@primer/behaviors`, `@primer/live-region-element`, Primer MDX docs | **MIT License** (`Copyright GitHub, Inc.`) | Free commercial & OSS use, modification, redistribution with MIT notice; Octicons free as UI icons across compatible projects | Misrepresenting MIT docs verbatim as original authorship without attribution; failing to include MIT notice if shipping MIT packages |
| `Mona Sans`, `Mona Sans Mono` fonts | **SIL Open Font License v1.1** (`Copyright 2022–2023 The Mona Sans Project Authors`) | Free web/app font embedding, bundling, modification | Selling font files standalone; using Reserved Font Name `"Mona"`/`"Mona Sans"` for modified derivative fonts; claiming GitHub endorsement of derivatives |
| `GITHUB®`, `INVERTOCAT`, `OCTOCAT®`, `Mona`, `Ducky`, `brand.github.com` Brand Guidelines PDF & illustrations/animations | **Proprietary / Registered Trademarks & Copyright (`© 2026 GitHub, Inc.`)** | Linking to GitHub repos/profiles; secondary integration badges obeying size/contrast/clearspace rules; internal/approved agency GitHub brand execution | Using Invertocat/Octocat as a non-GitHub app/org logo; altering/distorting/recoloring logos; placing logos on unapproved partner swag; invoking mascots in sales/billing/serious contexts without GitHub Brand Studio approval |

## Archived, deprecated, and superseded surfaces

Agents must explicitly distinguish active recommendations from retired surfaces:

- **`primer/design` repository**: Officially archived **July 2, 2025**. Its `/product/` and `/accessibility/` routes migrated into the live `primer.style` codebase, while `/native/` (gh CLI, Desktop, Mobile guidelines) was **retired and redirected to `/`**. Native rules in `references/product/10-iconography-and-content-ux.md` are explicitly preserved as archived historical reference.
- **Rails `@primer/view_components`**: Currently in **maintenance mode** (no new major architectural expansion compared to React + Primitives CSS Modules).
- **Product (`@primer/react`) deprecations**: `<Box>` and `sx` prop (migrate to CSS Modules + CSS custom properties), `CircleBadge`, `TextInputWithTokens`, Toast notifications (banned), outline-style Octocat empty-state art (`brand.github.com/graphic-elements/mascots/`).
- **Brand (`@primer/react-brand` & `brand.github.com`) deprecations**:
  - `Bento` component (deprecated -> use `CTABanner` or `River`)
  - `IDE` marketing code-editor component (deprecated -> removal scheduled)
  - `Button` `hasArrow` prop, legacy loose `Hero` button children (use `Hero.ButtonGroup`), legacy `River.Eyebrow` aliases (use `EyebrowText`)
  - Standalone **Copilot hero logo** (deprecated 2025) and **Copilot mascot head** (retired early 2026)

## Private, staff-only, and inaccessible sources

The following referenced documentation or tools exist behind GitHub internal authentication or private repositories and **cannot** be audited publicly; `primer-skill` refuses to guess their contents:

| Restricted Resource | Referenced From | Gap Impact |
| --- | --- | --- |
| `github/brand.github.com` & `github/primer-docs` source repos | Live site links / edit links | Internal site build scripts & unpublished drafts inaccessible; mitigated by crawling all `415` `primer.style` and `32` `brand.github.com` live pages directly. |
| **GitHub Voice & Tone guide** (`how-to-write-at-github.md` / `content.md` / Google Doc) | `brand.github.com/brand-identity/voice-and-tone/` | Detailed editorial grammar tables, channel voice sliders, and marketing campaign tone matrices are staff-only; mitigated via public brand attributes + Product UI copywriting rules (`product/10`). |
| `github/accessibility`, `github/scenarios`, `github/github-ui` (`ui.githubapp.com`) | `primer.style` internal references | Internal staff training repos, unreleased scenario drafts, and Rails monolith Staff UI docs unavailable; only public Scenario Patterns (`Copy`, `Create`, `Customize`, `Delegate`, `Delete`, `Edit`, `Filter`, `Search`, `View`) are covered. |
| Brand Studio Figma kits, PowerPoint templates (`Presentations`), Brand Asset Generator app, eBook templates, Invertocat video source files | `brand.github.com` (`getting-started`, `logo`, `presentations`, `eBooks`, `motion`) | Vector asset files, exact geometric clearspace multipliers (`x`-grids), and binary motion AE/Lottie sources require GitHub staff/partner credentials. |

## Source discrepancies and documentation bugs observed

Direct inspection uncovered several mismatches between prose docs and underlying implementations; `primer-skill` documents both rather than papering over contradictions:

1. **`Heading` live doc vs `Heading.module.css` (`@primer/react`)**: Live doc claims `Heading` renders `"without any default styling"`, whereas `Heading.module.css` applies `margin: 0`, `font-size: var(--text-title-size-large)`, and semibold `600` by default.
2. **`Text` live doc stale note (`@primer/react`)**: Live `product__components__text.md` claims `size` and `weight` examples await upgrade despite `Text.module.css` already implementing `data-size` and `data-weight`.
3. **Theme count (`9` vs `14`)**: `color-usage` documents 9 core themes while `@primer/primitives` builds 14 mode/theme targets (including `dark_tritanopia`, `light_tritanopia`, and contrast variants).
4. **Swatch step offset (`brand.github.com` vs `@primer/brand-primitives`)**: Brand Toolkit tables number `Gray 1–6` and `Green 1–6` (`Green 4 = #0FBF3E`), whereas `@primer/brand-primitives` uses `0–9` token scales where `#0FBF3E` sits at `green-5` (light) and `green-4` (dark).
5. **Lifecycle naming transition**: Live Product onboarding docs standardize `Private preview` / `Public preview` / `General availability (GA)` / `Closing down`, whereas legacy docs and props still mention `Alpha` / `Beta`.
6. **Live route & prop typos**: `primer.style/product/components/nav-list/guidlines/` uses the misspelled segment `guidlines`; `FormControl` MDX mentions `ValidationValidaitonStatusProp`; `brand.github.com` slug anchor uses `Imaginitive`; `Tiles.tsx` takes `variant="gridlines"` (plural) where sibling components use `"gridline"` (singular); `Brand` `BreakoutBanner/index.mdx` still contains a legacy caption `"prefer Bento instead"` despite `Bento`'s deprecation banner.
7. **`RiverStoryScroll` export gap**: Exported in `primer/brand/packages/react/src/index.ts` and implemented with reduced-motion handling, yet missing a published component documentation page in `apps/next-docs/content/components/`.
8. **Vector-outlined Brand PDF (`GitHub-Brand-Guidelines-2026.pdf`)**: Embedded outline paths prevent scriptable text extraction of print clearspace and typographic spread metrics beyond headings/TOC (`~1,680` characters extractable).

## Gaps, caveats and source issues

- **Point-in-time snapshot**: `primer.style` and `brand.github.com` evolve continuously (`Octicons`, `@primer/react` CSS Module migrations, Brand Flexsuite rollouts). Agents working in a repo that declares `@primer/react` or `@primer/react-brand` locally should prefer the installed package's bundled `llms.txt` / `@primer/mcp` / `@primer/brand-mcp` metadata when checking exact patch-level props.
- **Cross-file traceability**: Every factual claim across `references/product/01..10`, `references/brand/01..04`, and `references/cross/01..03` includes `Sources:` pointing back to exact live URLs and repository file paths verified above.
