# Primer Brand React Components and Brand in Action

> Indexes all 55+ content components and 9 form primitives in `@primer/react-brand`, installation and MCP tooling (`@primer/brand-mcp`), layout composition guidelines (`Hero`, `River`, `Card` vs `Pillar`, `CTABanner`, `SubdomainNavBar`, `MinimalFooter`), deprecation status, and channel rules from `brand.github.com/brand-in-action/`.

## Contents

- [Key takeaways](#key-takeaways)
- [Architecture, setup, and `@primer/brand-mcp`](#architecture-setup-and-primerbrand-mcp)
- [Core marketing page anatomy and composition rules](#core-marketing-page-anatomy-and-composition-rules)
- [Complete `@primer/react-brand` component catalog](#complete-primerreact-brand-component-catalog)
- [Component usage constraints and common traps](#component-usage-constraints-and-common-traps)
- [Brand in Action across channels](#brand-in-action-across-channels)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- **[Universal]** Marketing landing pages follow a clear narrative arc: **Header navigation** -> **Hero** (core value prop + CTA) -> **Social proof** (`LogoSuite`) -> **Feature/value storytelling** (`River` / tabs / cards / pillars alternating narrative weight) -> **Pricing / Comparison** -> **FAQ / Footnotes** -> **Closing conversion** (`CTABanner` or `CTAForm`) -> **Global Footer**.
- **[Universal]** Maintain sharp semantic separation between **Cards** (high-priority, interactive navigation in groups of 2–5), **Pillars** (lower-weight feature bullets up to 6 with images or 9 with icons), and **Rivers** (paired text + visual storytelling `<= 160` characters, not stacked endlessly without a visual break).
- **[Brand]** Use `@primer/react-brand` (v-synced docs bundled at `node_modules/@primer/react-brand/llms.txt`) *only* for marketing and editorial sites; use `@primer/react` for authenticated product apps. Never import both stylesheet sets into the same document surface.
- **[Brand]** **Deprecations to avoid**: Both **`Bento`** (use `CTABanner` or `River`) and **`IDE`** (marketing code simulator) are officially deprecated and slated for removal; `Button`'s `hasArrow` prop, `Hero`'s loose action children (use `Hero.ButtonGroup`), and `River`'s `Eyebrow` sub-component aliases (`use EyebrowText`) are also deprecated.
- **[Brand]** In `Section`, `Hero`, `River`, `RiverAccordion`, `RiverBreakout`, `SectionIntroStacked`, `LogoSuite`, `FAQ`, and `Tiles`, Flexsuite / gridline layouts pair `SubdomainNavBar` + `MinimalFooter` with `variant="gridline"` / `"gridline-expressive"` (`"gridlines"` on `Tiles`) and consistent `align="start"` rivers.
- **[Brand]** Physical and cross-media Brand in Action rules require WCAG AA accessible tagged PDFs for eBooks (minimal distraction inside body spreads), at least one tactile non-printed architectural material (`wood`, `metal`, `mesh`) in physical event booths, and keeping Universe annual identities strictly bounded to event promos rather than overwriting core web brand.

## Architecture, setup, and `@primer/brand-mcp`

`@primer/react-brand` ships modular CSS Modules + compiled CSS primitives tailored for React marketing sites (supporting SSR/Next.js App Router ESM imports):

- **Stylesheet requirement**: Import `@primer/react-brand/lib/css/main.css` (which bundles `reset.css`, base/functional brand tokens, and `.visually-hidden` utilities via `css/stylesheets.ts`), or import ESM per-component stylesheets alongside `ThemeProvider` (`colorMode="light" | "dark" | "auto"`).
- **Layout `Box` divergence**: Whereas `@primer/react` deprecated `Box` and `sx` in Product UI, `@primer/react-brand` still exports and actively documents **`Box`**, **`Grid`**, **`Stack`**, and **`Section`** for marketing page composition.
- **Version-pinned agent assistance (`@primer/brand-mcp`)**:
  - Reads local bundled `node_modules/@primer/react-brand` metadata rather than drifting against newer web docs.
  - Exposes 8 tools: `primer_brand_setup`, `primer_brand_page_design` (shipping Flexsuite reference templates including `FlexSuiteAIOverview`, `FlexSuiteSecurityCategory`, `FlexSuiteAIDetailsPlaylist`), `primer_brand_component`, `primer_brand_examples`, `primer_brand_tokens`, `primer_brand_asset` (Octicons + Octovisuals), `primer_brand_docs`, and `primer_brand_review` (static validator against invalid props, hardcoded values, raw product images, deprecated components, and AI-slop tells).

Sources: `primer/brand` (`packages/react/README.md`, `packages/react/src/css/stylesheets.ts`, `packages/mcp/README.md`, `apps/next-docs/content/getting-started/index.mdx`, `apps/next-docs/content/getting-started/esm.mdx`).

## Core marketing page anatomy and composition rules

Verified `@primer/brand-mcp` (`page-design.md`) and `brand/apps/next-docs` establish concrete structural rules for assembling a GitHub marketing landing page:

1. **Global shell**: Open with **`SubdomainNavBar`** (plus optional `EyebrowBanner` promo or sticky intra-page **`AnchorNav`** / **`SubNav`** beneath) and close with **`MinimalFooter`** (up to 5 `MinimalFooter.Link` items, configurable `socialLinks`, legal footnotes via `MinimalFooter.Footnotes`). Note: `SubdomainNavBar`'s `variant="featured"` is restricted to approved internal GitHub sites.
2. **Hero above the fold**: Full-viewport header via **`Hero`**. Keep heading `<= 2` lines (renders `<h1>` by default; check heading hierarchy before overriding `as`) and description `3–4` lines (`default` or `muted` only when high contrast holds over clean neutral ground—never over imagery). Include up to two `Button` / `ActionMenu` actions inside `Hero.ButtonGroup` (auto-applies `primary` to #1 and `secondary` to #2). Note: `Hero` `position` / `padding` media options apply to `gridline` variants cleanly while `default` ignores non-default media padding.
3. **Narrative flow & anti-monotony**: Introduce multi-item feature sections with **`SectionIntro`** (`1` short paragraph max, optional 1 emphasized phrase, optional link; never stack two `SectionIntro`s alone). Break up repetitive vertical stacks of **`River`** (`50:50` ratio default, `60:40` for hero visuals) using a **`RiverBreakout`** between two standard rivers (never place two `RiverBreakout`s or a breakout first in stack), or pivot into **`Pillar`** / **`Card`** grids, **`RiverAccordion`**, **`RiverBreakoutTabs`**, or **`RiverStoryScroll`**.
4. **Closing conversion (`CTABanner` vs `CTAForm`)**:
   - Use **`CTABanner`** at the bottom (or cleanly between deep sections—*never* immediately after Hero) to echo top Hero actions (`primary`/`secondary` ButtonGroup). Keep solid default background if shadow is enabled (removing background while keeping shadow blurs legibility); in `variant="balanced"`, a **`CTABanner.Image`** child is required (in `variant="minimal"`, images are ignored).
   - Use **`CTAForm`** instead of `CTABanner` when the primary goal is capturing input (newsletter/waitlist) rather than linking out; keep input width proportionate to expected data, wrap with clear surrounding context, and treat an attached consent `Checkbox` as mandatory to submit.

Sources: `primer/brand` (`packages/mcp/content/page-design.md`, `apps/next-docs/content/components/{Hero,River,CTABanner,CTAForm,SectionIntro,SubdomainNavBar,MinimalFooter}/index.mdx`, `react.mdx`).

## Complete `@primer/react-brand` component catalog

Grouped functionally from `packages/react/src/index.ts` and `apps/next-docs/content/{components,forms}`:

### Layout, primitives, and wrappers

| Component | Purpose & Key Notes |
| --- | --- |
| `ThemeProvider` | Scopes `light`, `dark`, or `auto` (`data-color-mode`) across whole pages or alternating dark/light bands |
| `Section` | Semantic `<section>` / `<div>` container (`variant`: `default \| gridline`, responsive `paddingBlockStart`/`End`, `backgroundColor`, `backgroundImageSrc`; `rounded` incompatible with `gridline`) |
| `Grid` | 12-column responsive CSS grid (`16 / 32 / 48px` gutters) |
| `Stack` | Vertical/horizontal flex layout with `condensed / normal / spacious` or token gaps |
| `Box` | Brand-supported low-level container with spacing, border, background tokens (kept in Brand even after Product deprecated `Box`) |
| `Prose` | Applies Primer Brand typography spacing and contrast styles to raw CMS/HTML prose |
| `AnimationProvider`, `Animate` | Scroll/viewport-triggered entrance choreography (`100ms` stagger default, reduced-motion safe) |
| `FrostedGlassVFX` | Translucent blur card wrapper (`intensity`: `low \| medium \| high`); use sparingly—never mimic manually with arbitrary `backdrop-filter` |

### Navigation, banners, and typography

| Component | Purpose & Key Notes |
| --- | --- |
| `SubdomainNavBar` | Fixed top-level site navbar with optional search dialog (`keyboardShortcut`), dropdowns, and CTA slots (`variant="featured"` internal-only) |
| `SubNav` | Secondary horizontal navigation rail beneath primary bar with mobile overflow menu (`menuLabels` i18n) and optional `SubNav.Action` |
| `AnchorNav` | Sticky in-page section jumper (`#section-id`) + up to 2 actions |
| `NavList` | Vertical sidebar navigation with optional expandable groups |
| `Breadcrumbs` | Site hierarchy trail |
| `Pagination` | Sequential linked multi-page pagination |
| `EyebrowBanner` | Top-of-page promo bar (`Label`, `Visual` / `Octicon`, `Heading`, `SubHeading`) linking to announcements/events |
| `BreakoutBanner` | Mid-page promo highlight linking strictly to *supporting* content (not final conversion—use max `<= 1` per page, center/middle placement, `<= 3` line heading) |
| `Heading` | Semantic `h1`–`h6` with `size="display" \| "subhead-large" \| "subhead-medium" \| "1".."6"`, `weight`, `font="mona-sans" \| "hubot-sans" \| "monospace"` |
| `Text`, `EyebrowText`, `TextCursorAnimation` | Body typography (`size="100".."1000"`, `default`/`muted`), terminal typewriter cursor animation for eyebrows |
| `InlineCode`, `InlineLink`, `Link` | Inline code highlights, semantic text links, directional brand links |
| `OrderedList`, `UnorderedList` | Styled editorial/marketing lists (recommended inside `FAQ` answers over walls of text) |
| `Footnotes` | Legal/source citation blocks paired with superscript references |

### Storytelling, showcases, and data proof

| Component | Purpose & Key Notes |
| --- | --- |
| `Hero` | Above-the-fold page opener (`align`: `start \| center`, `variant`: `default \| gridline \| gridline-expressive`) |
| `SectionIntro`, `SectionIntroStacked` | Thematic `<section>` header (`Label`, `Heading`, `Description`, `Link`); `SectionIntroStacked` adds grid-aligned itemized summary blocks + `gridline` variant |
| `River`, `RiverBreakout`, `RiverAccordion`, `RiverBreakoutTabs`, `RiverStoryScroll` | Flagship feature pairings (`River` text + visual; `RiverBreakout` full-width visual highlight; `RiverAccordion` expandable list + synced media; `RiverBreakoutTabs` desktop card tabs collapsing to accordion on mobile; `RiverStoryScroll` sticky visual scrollytelling exported in `index.ts`, undoc'd in `next-docs` MDX) |
| `Card` | Navigational summary tiles (`variant`: `default \| minimal`, `ctaVariant`: `default \| arrow \| none`) — always group `2+` (`3–4` typical) |
| `Pillar` | Lighter non-navigational feature blocks (`icon` or `image` + `Heading` + `Description` + optional link) |
| `Tiles` | Grid of partner/tool logos or icons (`variant="gridlines"` plural!) |
| `LogoSuite` | Credibility logobar for `4`–`12` logos (`muted` auto for `6+`, `emphasis` auto for `<= 5`, `marquee` animated strip up to `12`, optional `gridline`/`gridline-expressive` divider) |
| `Testimonial`, `TestimonialGroup` | Customer quote (`<= 3–4` lines, single highlight, real avatar or org logo—never generic illustration, `<= 4` per group) + carousel wrapper (`CarouselPagination`) |
| `Statistic` | High-impact metric (`size`: `small \| medium \| large`, optional `variant="boxed"`, `Description` `accent` variant) |
| `Timeline` | Vertical connected bullet list (`<b>` renders duotone emphasis; **requires `dir` attribute on `<html>`**) |
| `PricingOptions` | Plan tiers (`variant="default" \| "cards"`, `Price`, `FeatureList`, `Label` recommended tag, primary/secondary actions) |
| `ComparisonTable`, `FeatureComparisonTable` | Side-by-side capability matrix (`ComparisonTable` with optional `Footnote`; `FeatureComparisonTable` with collapsible plan groups, `included` icons, row descriptions) |
| `FAQ` / `FAQGroup` | Expandable Q&A accordions (`variant="gridline"` supported, customizable heading levels, `<= 2–3` paragraph answers, **no images/media inside answers**) |
| `MediaPlaylist`, `VideoPlayer`, `MinimalVideoPlayer` | Video showcase (tabbed YouTube/embed playlist, full accessible HTML5 video player with `.vtt` tracks & audio description support, silent auto-looping demo player respecting `prefers-reduced-motion`) |
| `Tabs`, `Accordion` | Interactive content panels (`variant="accent" \| "underline"` on `Tabs`) and disclosure wrappers |
| `Button`, `ButtonGroup`, `IconButton` | Primary (`1` per view unless repeated in header/footer), Secondary (default), Subtle, and Block buttons; always sentence case, single line, preserve `:focus` ring |
| `Avatar`, `Badge`/`Label`, `Token`, `Tooltip`, `Spinner`, `Icon`, `Image` | Compact identity/status metadata, loading indicator, Octicon circular/square container, responsive picture wrapper |
| **`Bento` (DEPRECATED)** | **Deprecated**: Use `CTABanner` or `River` instead (source-order vs visual-order accessibility risks also flagged) |
| **`IDE` (DEPRECATED)** | **Deprecated**: Will be removed in a future release (previously simulated code editor + AI chat in marketing contexts) |

### Forms (`@primer/react-brand`)

`FormControl` (manages label, `Hint`, `Validation` ARIA bindings across breakpoints), `Checkbox`, `CheckboxGroup`, `Radio`, `RadioGroup`, `Select`, `Textarea` *(exported component; MDX directory named `TextArea`)*, `TextInput`, `ToggleSwitch` (boolean immediate setting).

Sources: `primer/brand` (`packages/react/src/index.ts`, `apps/next-docs/content/components/*/index.mdx`, `apps/next-docs/content/forms/*/index.mdx`).

## Component usage constraints and common traps

- **Card vs Pillar decision matrix**:
  - **Interaction**: `Card` is navigational (click navigates cleanly to a target page); `Pillar` is primarily informational text + optional link.
  - **Group size**: Use `Card` in tight rows of `2`–`5` (`<= 3` centered; `4+` switch to `start` alignment so lines read cleanly). Never render an isolated single `Card` (`CTABanner` belongs there). Use `Pillar` for larger feature lists up to **`9`** items with icons, or **`3`–`6`** items when carrying images (and match icon/image modality and aspect ratio across all siblings in the row).
- **Button hierarchy**: Secondary is the default button appearance (`Button` docs). Reserve **`Primary`** (`variant="primary"`) strictly for the hero/singular decisive action—at most one visible primary CTA per viewport band unless mirrored cleanly in top header + bottom footer. Use `leadingVisual` Octicon to clarify action semantics (Search, Play) and `trailingVisual` strictly for menu/picker affordances (Chevrons); never turn icons into decorative leading fluff.
- **Heading outline integrity**: Components like `Hero` default to `<h1>`, while `MediaPlaylist`, `FAQ`, `SectionIntro`, and `RiverBreakoutTabs` accept `as` (`h2`–`h6`) props. Audit generated DOM order so pages never skip heading levels or create duplicate `<h1>` landmarks.
- **No media inside FAQ**: Keep `FAQ` answers concise prose + lists (`OrderedList`/`UnorderedList`); switch to `River` or `Card` grids when visuals or diagrams are required.
- **`Timeline` `dir` gotcha**: Always verify `<html dir="ltr">` (or `rtl`) is set or `Timeline` layout styling breaks.

Sources: `primer/brand` (`apps/next-docs/content/components/{Card,Pillar,Button,FAQ,Timeline,Hero,BreakoutBanner,LogoSuite,Testimonial}/index.mdx`).

## Brand in Action across channels

`brand.github.com/brand-in-action/` extends the design identity beyond React landing pages:

| Channel | Core guidelines & constraints |
| --- | --- |
| **Web (`web`)** | Anchored on `@primer/react-brand` + Gridline/Flexsuite patterns (`ThemeProvider`, `--brand-*`, Storybook verification). |
| **Social (`social`)** | Dynamic/adaptable visual storytelling: official event/speaker promos must match event branding; shop/swag promos stay bold with clear branding; casual human community engagement posts require no formal brand lockup; contests allowed to flex personality. |
| **Experiential & Events (`experiential`)** | Grounded in developer culture (*"take our work seriously, not ourselves"*). Flagship **GitHub Universe** receives a bespoke annual identity that must **not** overwrite general evergreen brand rules. Event booths must integrate identity with restraint, feature reusable modular pieces across booth footprints, engage hands-on, stay physically accessible, and **always include at least one tactile non-printed architectural material** (`wood`, `metal`, `mesh`, etc.) rather than vinyl walls everywhere. |
| **eBooks & Whitepapers (`eBooks` + `foundations/accessibility`)** | Expressive domain-appropriate covers respecting enterprise editorial gravity; **calm distraction-free interior reading spreads** (subtle typography, clear margins, *no* bright marketing illustrations interrupting long-form prose); fully tagged PDFs with structural reading order, alt text, and sufficient type scale. |
| **Presentations (`presentations`)** | Governed by staff-only PowerPoint templates (`github.com` internal access only). |
| **Swag & Shop (`swag`)** | High-quality tactile apparel/collectibles rather than cheap promo giveaways; lean on developer lore where stickers and small desk objects carry detail. |

Sources: `https://brand.github.com/brand-in-action/{web,social,experiential,eBooks,presentations,swag}/`, `https://brand.github.com/foundations/accessibility/`.

## Agent application notes

- **When scaffolding `@primer/react-brand` pages**: Start from `Section` -> `Grid` / `Stack` structure, wrap in `<ThemeProvider>`, load `main.css` cleanly, use `SubdomainNavBar` + `Hero` + `SectionIntro` + `River` / `Pillar` / `Card` + `CTABanner` + `MinimalFooter`, and double-check that deprecated `Bento`/`IDE`/`Hero` legacy children/trailing-`Button.hasArrow` patterns are **never** generated.
- **When designing non-GitHub marketing pages**: Translate the structural wisdom (hero line-count caps, strict 1–2 primary CTA hierarchy per screen, Card-vs-Pillar sizing taxonomy, `CTABanner` at journey end rather than page top, calm long-form PDF/blog interiors free of loud promo clutter) into the project's own component library and tokens.

## Gaps, caveats and source issues

- **`RiverStoryScroll` documentation gap**: Exported publicly from `packages/react/src/river/index.ts`, uses reduced-motion checks in TSX, yet lacks a published `apps/next-docs/content/components/RiverStoryScroll/` MDX doc page in this snapshot. Inspect `RiverStoryScroll.tsx` before deploying it.
- **Staff-restricted Brand assets**: Presentation `.pptx` decks, eBook layout templates, and Figma component libraries are access-gated to GitHub employees/approved agencies.
- **Missing explicit `brand-in-action/web` samples**: The live `brand.github.com/brand-in-action/web/` page currently states *"Samples coming soon"*, deferring directly to `primer.style/brand` and Storybook.
- **`IDE` vs `@primer/brand-mcp` docs**: Some older component descriptions or Storybook links still reference `Bento` (note `BreakoutBanner/index.mdx` even still includes a legacy caption `"prefer Bento instead"` despite `Bento`'s deprecation banner in `Bento/index.mdx`). Always obey `Bento`'s and `IDE`'s explicit deprecation notices rather than stale cross-links.
