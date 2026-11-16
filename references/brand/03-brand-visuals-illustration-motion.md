# Brand Visuals, Illustration, Textures, Mascots, and Motion

> Covers GitHub Brand's graphic expression spectrum (from key art to quiet wallpaper textures), the three illustration pillars, Mona vs Ducky mascot boundaries, stylized Product UI framing rules, Spot icons vs Octicons, and Brand Motion principles, easing tokens, and animation patterns.

## Contents

- [Key takeaways](#key-takeaways)
- [Illustration expression spectrum and pillars](#illustration-expression-spectrum-and-pillars)
- [Textures: contribution graph and dithering](#textures-contribution-graph-and-dithering)
- [Product UI screenshots in marketing](#product-ui-screenshots-in-marketing)
- [Mascots: Mona and Ducky](#mascots-mona-and-ducky)
- [Iconography, spot icons, and diagrams](#iconography-spot-icons-and-diagrams)
- [Brand motion identity, tokens, and patterns](#brand-motion-identity-tokens-and-patterns)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- **[Universal]** Modulate visual loudness intentionally along an **expression spectrum**—rich key art for flagship announcements, quiet structured patterns for incremental updates—and never place decorative textures and foreground text inside the same unbuffered contrast zone.
- **[Universal]** Marketing product screenshots should simplify distracting chrome (`sidebars`, `footers`, dead buttons), zoom onto the actual value-bearing interaction, frame with a hairline contrast border matching backdrop geometry, and describe code/content in `alt` text.
- **[Brand]** GitHub illustrations rest on the **Contribution Square** motif across three thematic pillars: **Core GitHub** (contribution-grid geometry + mascots), **AI & Agents** (sine waves, pearl textures, exclusive pearl Mona), and **Security & Privacy** (shield Octicon + subtle blur/obscured treatments).
- **[Brand]** Textures (**Contribution Graph** and **Dithering**) must *always* act like quiet background wallpaper—never scatter squares off-grid, never bloat sub-cell elements larger than one cell, and **never combine dithering and contribution-graph patterns together**.
- **[Brand]** **Mona** and **Ducky** are community delight elements only: never use mascots as logos, in sales/enterprise/customer-support/training flows, to interrupt tasks, or alongside serious subjects (money, security incidents, crises, apologies). All public mascot appearances require Brand & Marketing Design approval.
- **[Brand]** Brand motion follows three outcomes (**Facilitate**, **Engage**, **Impact**), five named transition patterns (`Container transform`, `Container masking` for celebrations/confirmations only, `Lateral`, `Enter and exit`, `Simple ease`), and calibrated `@primer/brand-primitives` durations (`0.2s`, `0.3s`, `0.6s`, `0.7s`) with `cubic-bezier(0.16, 1, 0.3, 1)` / `glide` easing and `AnimationProvider` `100ms` stagger.

## Illustration expression spectrum and pillars

GitHub refreshes its illustration library annually around Universe while maintaining a stable underlying framework:

1. **Modulate visual volume across the spectrum**:
   - **High expression (Key Illustrations)**: Reserved for flagship brand announcements and hero moments. Rendered primarily in a light-mode palette featuring mascots, structured contribution-graph compositions, dithering accents, and subtle developer artifacts.
   - **Low expression (Supportive Patterns)**: Reserved for product changelog notes, educational articles, and sub-pages where imagery supports typography without competing for attention.
2. **Foundational building block — The Contribution Square**: Rather than pasting literal profile heatmaps everywhere, GitHub abstracts the contribution cell as a modular geometric unit that repeats, scales, and transforms on-grid.
3. **Three Brand Illustration Pillars**:

| Pillar | Visual vocabulary | Subject focus |
| --- | --- | --- |
| **Core GitHub** | Grid-aligned contribution-square geometry, clean modular blocks, Mona & Ducky | Platform fundamentals, developer workflow, open-source community |
| **AI & Agents** | Sine waves, computational motion trails, **pearl textures**, pearl-colored Mona (AI-exclusive) | Copilot, agentic delegation, intelligence and speed |
| **Security & Privacy** | **Shield Octicon** motif, layered blur effects, obscured/masked color blocks | Code scanning, secret protection, defensive risk reduction |

Sources: `https://brand.github.com/graphic-elements/illustration/`.

## Textures: contribution graph and dithering

Two signature background textures anchor GitHub's visual language:

- **Contribution graph motif**: Abstracted or literal grid cells from a developer's yearly commit heatmap. Minimal treatments (such as speaker cards or slides) can distill it to **four squares in a row or column**. Always source pre-made textures rather than sketching ad-hoc cells from scratch.
- **Dithering texture**: Deeper machine-code abstraction evoking binary `0`s and `1`s and halftone bitmaps.

### Hard texture rules

1. **Wallpaper, never key art**: Both textures may vary in scale or subtlety, but must stay strictly behind foreground elements as ambient structure.
2. **Respect grid alignment**: Do not break the grid, add erratic spacing, or explode contribution squares into floating confetti/scatter shapes.
3. **Containment inside cells**: Never scale sub-elements beyond a single grid cell boundary (`everything should fit within a cell`).
4. **Never combine textures**: Do not stack dithering textures and contribution-graph patterns in the same visual composition (`brand.github.com/graphic-elements/textures/`).

Sources: `https://brand.github.com/graphic-elements/textures/`.

## Product UI screenshots in marketing

`@primer/react-brand` docs (`page-design.md`, `rules.ts`) and `brand.github.com/graphic-elements/product-ui/` tightly codify how product interfaces appear in marketing layouts:

- **Never render bare `<img>` UI shots on flat page ground**: Wrap captured UI screenshots inside a two-layer neutral or themed backdrop frame using gradient fades or directional **dither** patterns whose visual direction points toward the key UI focal area (for example pointing toward an upper-right Copilot action). Apply gradient masks so textures recede behind the interface.
- **Border matched to dither grid**: Apply a hairline container border (`border-radius` + subtle gradient/semi-opaque stroke—never 100% transparent) whose stroke thickness harmonizes visually with the pixel/square size of the underlying dither pattern. This technique also frames logo tiles or multi-panel cards cleanly.
- **Neutral ground default**: Most stylized product captures sit over neutral canvas (`canvas.default` / `canvas.subtle`); reserve colored campaign backdrops for major launches matching product pillar colors.
- **Edit ruthlessly for clarity**: Crop, zoom, or gently offset layers to spotlight the single takeaway feature. Strip distracting outer navigation sidebars, footers, stray borders, non-essential buttons, and unreadable filler code while keeping real GitHub UI styling recognizable. Keep spacing identical across multi-image sequences.
- **Meaningful `alt` descriptions**: Screen reader alt text must describe both what UI controls are depicted *and* any key visible code or demonstration outcome.

Sources: `https://brand.github.com/graphic-elements/product-ui/`, `primer/brand` (`packages/mcp/content/page-design.md`, `packages/mcp/src/review/rules.ts`).

## Mascots: Mona and Ducky

GitHub's mascots embody developer culture—**Mona** (the Octocat pioneer representing open source and community; appears in default colors, bright purple for dark mode, or exclusive pearl finish for AI/Agents) and **Ducky** (rubber-duck debugging companion, deployed more sparingly).

### Four Octocat heritage styles (and deprecation status)

- **Original Octocat**: Legacy flat illustration style; retained strictly for historical throwbacks, not new campaigns.
- **Octocat 2.0**: Current expressive 3D-ready narrative/sticker model.
- **Outline style**: Used previously in product empty states and mobile; **currently being deprecated**.
- **Monamoji**: Reaction/custom emoji only; **must not** represent GitHub's official brand voice.
- *(Reminder)*: The **Copilot mascot head** was completely retired in early 2026 (`references/brand/01-brand-identity-and-voice.md`).

### Usage guardrails (less is more)

| Allowed (`Do`) | Prohibited (`Don't`) |
| --- | --- |
| Developer community moments (events, merch, collectibles, celebratory social/internal culture) | Acting as a brand/team/app logo or co-branded partner badge |
| Inspiring and entertaining students (without acting as the primary instructor) | Appearing in sales, pricing, enterprise procurement, customer support, or technical training |
| Internal culture (Slack reactions, staff swag, milestone celebration stickers) | Interrupting active user workflows or filling awkward empty whitespace |
| *(Approval rule)* Public-facing uses cleared with GitHub Brand & Marketing Design | Any serious or sensitive topic: money, billing, security incidents, crises, apologies, politics |

Sources: `https://brand.github.com/graphic-elements/mascots/`.

## Iconography, spot icons, and diagrams

- **Octicons (`@primer/octicons-react`)**: Primary shared throughline between Product UI and Brand surfaces (`12px` inline micro, `16px` UI standard, `24px` hero/feature anchors). In Brand, Octicons enhance scannability inside bullet lists, `<Pillar>` headers, `<Card>` accents, or key art tags without upstaging contribution art or mascots. Note: `@primer/react-brand`'s `<Icon>` wrapper accepts Octicons with styled neutral or semantic container badges.
- **Spot icons**: Expressive mid-size icons bent from GitHub's gitline / commit-circle geometry to echo product icons. They sit between tiny Octicons and full key illustrations—use to give topical meaning to feature columns/cards with restrained neutral + single hero color palettes, and **never shrink Spot icons down to UI icon scale (`16px`–`24px`)**.
- **Charts, diagrams, and infographics**:
  - *Charts & graphs*: Prioritize accuracy, high contrast, clear labels, non-color redundancy (patterns/labels), and `alt` descriptions. Explicitly ban decorative illustrations, ambient gradients, or colliding adjacent bright hues inside data plots.
  - *Diagrams*: Clean, technical, instructional linework; restrained color blocking only for key product pillars (e.g., Copilot, Security) or information grouping.
  - *Infographics*: May pair icons, crisp numerals (`Statistic` component), and structured layout to communicate comparative human impact—bolder than charts, still grounded in exact data.

Sources: `https://brand.github.com/graphic-elements/iconography/`, `https://brand.github.com/graphic-elements/diagrams/`, `https://brand.github.com/foundations/accessibility/`.

## Brand motion identity, tokens, and patterns

While Product UI motion prioritizes near-instant mechanical feedback (`<= 300ms` transitions, `500ms` ceiling), Brand Motion bridges precision and delight across marketing websites, product demo animations, and live events:

### Three motion outcomes

1. **Facilitate**: Direct eye tracking, clarify cause-and-effect state transitions, and make the next CTA clear (always prioritize this in utility/interactive marketing UI).
2. **Engage**: Establish organic/mechanical cadence that sustains momentum without overshadowing the copy.
3. **Impact**: Give landmark launches or hero reveals memorable signature expression.

### Motion timing & React primitives (`@primer/brand-primitives` + `@primer/react-brand`)

- **Duration tokens (`functional/animation/base.json`)**:
  - `faster`: `0.2s`
  - `fast`: `0.3s`
  - `default`: `0.6s`
  - `extended`: `0.7s`
  - (Interactive controls such as buttons remain `80ms` via `control` tokens.)
- **Easing curves**:
  - `default`: `cubic-bezier(0.16, 1, 0.3, 1)` (swift departure, silky architectural settle)
  - `glide`: `cubic-bezier(0.33, 1, 0.68, 1)`
- **Displacement presets**: Slide entries offset `±15px` (`slideInUp` / `Down` / `Left` / `Right`), keeping entrances controlled rather than flying across viewports.
- **`AnimationProvider` & `Animate`**: Wraps multi-element bands (`autoStaggerChildren`, default `staggerDelayIncrement: 100` ms, `animationTrigger="immediate" | "on-visible"`), automatically honoring `prefers-reduced-motion` (verified across `MinimalVideoPlayer`, `RiverStoryScroll`, and animation CSS tokens).

### Five Brand transition patterns

| Pattern | Mechanism & Intent | Ideal use cases |
| --- | --- | --- |
| **1. Container transform** | Morphs/expands source element continuously into destination | Modals, card-to-expanded view |
| **2. Container masking** | Wipes/reveals confirmation inside the triggering button/container bounds | **Strictly celebratory or confirmed outcomes** (Download, Copy, Save, Submit) |
| **3. Lateral** | Slides peer views horizontally along a shared structural rail | Top nav indicators, subnav, tabs, pricing tier switches |
| **4. Enter and exit** | Sequenced staggered entrance/exit showing hierarchy changes | Dropdown menus, drawers, overlays, popover lists |
| **5. Simple ease** | Crisp micro-opacity/scale/resize with minimal displacement | General UI hover, subtle state changes when richer choreography adds no meaning |

### Typography, logo, UI demos, and Mona Pixels

- **Type motion**: **Mona Sans** headlines reveal fluidly in clean masked blocks; **Mona Sans Mono** (`TextCursorAnimation` component) adopts mechanical terminal typing cadence paired with rotating contributor-colored cursors (commonly used for Hero/Testimonial eyebrows). **Never** apply out-of-the-box PowerPoint bounce, slap, spin, or roll effects unless directly animating a character's physical motion.
- **Mona Pixels**: Expressive retro pixel vignettes used both for brief marketing wait/processing states and celebratory completions (account created, file downloaded)—never overused as perpetual idle distraction.
- **Product UI demos in video**: Represent GitHub UI faithfully without distorting actual layout logic; calibrate speed to context (high energy for Universe event openers, deliberate legibility for technical live-streams; avoid faux "glitch" filters during live technical broadcasts where viewers read glitches as stream corruption).
- **Accessible media playback**: Loop ambient UI loops silently (`MinimalVideoPlayer`) with pause/play control and auto-suppression when `prefers-reduced-motion: reduce` is enabled; full audio/video players (`VideoPlayer`) require captions (`.vtt`) and an audio-described track or alternative link (`brand.github.com/foundations/accessibility/`, `VideoPlayer/index.mdx`).

Sources: `https://brand.github.com/motion/principles/`, `https://brand.github.com/motion/logo/`, `https://brand.github.com/motion/events/`, `primer/brand` (`packages/design-tokens/src/tokens/functional/animation/base.json`, `apps/next-docs/content/introduction/animation.mdx`, `apps/next-docs/content/components/MinimalVideoPlayer/index.mdx`, `apps/next-docs/content/components/VideoPlayer/index.mdx`, `packages/react/src/TextCursorAnimation/TextCursorAnimation.tsx`).

## Agent application notes

- **Non-GitHub marketing sites**: Borrow GitHub's *structural visual discipline*—clear backdrop container framing around UI screenshots (cropped to the key insight, surrounded by subtle texture/border with accessible `alt` descriptions), restrained single-tier expressive motion (`0.2s`–`0.6s`, smooth exponential ease-out, zero cheesy bounce/rubberband effects, `prefers-reduced-motion` gating), and keeping charts crisp and illustration-free. Replace contribution squares, dithering, and mascots with motifs authentic to the client's domain.
- **GitHub Brand builds**: Always pair UI screenshots with dithered/neutral container frames rather than raw images, respect the strict texture-isolation rule (`contribution graph` OR `dither`, never both together), pick mascot usage cautiously (`Brand Studio` approval + zero serious/sales placement), and use `@primer/react-brand`'s `AnimationProvider` / `Animate` rather than importing heavy third-party animation frameworks unnecessarily.

## Gaps, caveats and source issues

- **Binary motion clips & Figma art packs**: `motion/principles/`, `motion/events/`, and `graphic-elements/illustration/` illustrate transitions, seasonal tessellation palettes, and Mona Pixel GIFs via embedded web video/media assets; numerical token values recorded above come from inspecting `@primer/brand-primitives`' `animation/base.json` and component CSS directly.
- **`TextCursorAnimation` documentation gap**: `TextCursorAnimation` is exported in `packages/react/src/index.ts` and tokenized, though most visual motion rules reside in `brand.github.com/motion/principles/` rather than a dedicated `next-docs` page.
- **Outdated doc cross-reference**: `MinimalVideoPlayer` guidelines and `Hero`/`Section` screenshots in `brand.github.com` predate or mix with minor Universe seasonal motifs; Universe-specific motion/event visuals are explicitly marked non-normative for evergreen GitHub pages.
