# Design Review Checklist

> Concrete, scannable evaluation criteria synthesized from Primer Product guidelines, Primer Accessibility criteria, `@primer/react` linting rules, GitHub Brand Toolkit rules, and `@primer/brand-mcp` automated checks to evaluate generated product apps and marketing websites.

## Contents

- [Key takeaways](#key-takeaways)
- [How agents should run this review](#how-agents-should-run-this-review)
- [1. Scope, mode, and stack appropriateness](#1-scope-mode-and-stack-appropriateness)
- [2. Layout, spacing, and responsive structure](#2-layout-spacing-and-responsive-structure)
- [3. Accessibility and interaction integrity (WCAG 2.2 AA)](#3-accessibility-and-interaction-integrity-wcag-22-aa)
- [4. Design tokens, color contrast, and CSS hygiene](#4-design-tokens-color-contrast-and-css-hygiene)
- [5. Typography, hierarchy, and UX copy](#5-typography-hierarchy-and-ux-copy)
- [6. Forms, saving, loading, errors, and data density](#6-forms-saving-loading-errors-and-data-density)
- [7. Marketing / Brand craft and anti-AI-slop audit](#7-marketing--brand-craft-and-anti-ai-slop-audit)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- **[Universal]** Evaluate interfaces on structural usability, contrast, keyboard/screen-reader flow, honest system feedback, and brand-appropriate tone—never treat *"looks like GitHub"* as a passing grade unless actually building for GitHub.
- **[Universal]** Every failed check falls into either a **Blocking Defect** (WCAG 2.2 AA failure, keyboard trap, banned pattern like timed Toasts/hover-only actions, invisible focus, muted-on-muted contrast fail, destructive flow lacking confirmation, leaked GitHub trademarks on unrelated apps) or a **Polish Issue** (spacing drift, uneven Card copy lengths, missed progressive disclosure).
- **[Primer]** Inside `@primer/react` and `@primer/react-brand` codebases, enforce package-specific bans cleanly (`Box`/`sx` deprecated in `@primer/react` yet supported in `@primer/react-brand`; `Bento` and `IDE` deprecated in `@primer/react-brand`; `Toast` banned in Product UI).

## How agents should run this review

Apply sections selectively based on the audited project:
- **Every project**: Run Sections **1, 2, 3, 4, 5**, plus **Section 6** (if interactive app/forms/tables exist) and **Section 7** (if marketing/landing pages exist).
- **Non-GitHub projects**: Enforce `[Universal]` checks strictly; *skip* checks marked **`[GitHub-only]`** (and **fail** the review if GitHub marks, green `#0FBF3E`, Invertocat, or Mona leaked in without cause).
- **Primer React / Primer Brand projects**: Enforce both universal checks and **`[Primer]` / `[Brand]`** API/token constraints.

Sources: `references/cross/01-product-vs-brand-and-adaptation.md`, `primer/brand` (`packages/mcp/src/review/rules.ts`), `primer/react`.

## 1. Scope, mode, and stack appropriateness

- [ ] **Correct surface mode `[Universal]`**: Product workflows avoid promotional hero banners/marketing fluff; marketing landing pages avoid cramped dense form-table chrome.
- [ ] **Native stack respect `[Universal]`**: Non-Primer projects use their own component/CSS ecosystem cleanly without forcing `@primer/react` or `@primer/react-brand` imports.
- [ ] **No cross-package stylesheet pollution `[Primer]` / `[Brand]`**: `@primer/react` and `@primer/react-brand` CSS bundles are not imported into the same document surface.
- [ ] **Zero brand impersonation `[Universal]`**: Non-GitHub products use their own palette, typography voice, and domain iconography—no `#0FBF3E`, `#0969da` GitHub defaults, Invertocat, Octocat/Mona, Ducky, or contribution-graph wallpapers.
- [ ] **Copyright/trademark & Copilot retirement `[GitHub-only]`**: Official GitHub pages follow `GitHub` capitalization, approved lockups (`Invertocat + wordmark` externally), and contain zero retired 2025 Copilot hero logos or 2026 Copilot mascot heads.

Sources: `references/brand/01-brand-identity-and-voice.md`, `references/cross/01-product-vs-brand-and-adaptation.md`.

## 2. Layout, spacing, and responsive structure

- [ ] **Natural DOM reading order `[Universal]`**: Visual order matches DOM tab/screen-reader order (no `flex-direction: *-reverse` or CSS `order` reordering interactive flow).
- [ ] **Coarse viewport adaptation `<768` / `>=768` / `>=1400` `[Universal]`**: Narrow viewports preserve 100% of core functionality (via drill-down or bottom/side sheets—never deleted features or long nav lists dumped above primary content).
- [ ] **320px x 256px reflow `[Universal]`**: Page remains usable at 320px CSS width without two-axis scrolling or clipped controls.
- [ ] **Scroll-pane placement `[Universal]`**: Full-bleed split sidebars sit at the **start/left edge** in LTR (avoiding double adjacent scrollbars on the right).
- [ ] **No horizontal navbar sprawl or sticky product app header `[Primer]` / `[Universal]`**: Dense workspace headers do not eat scarce vertical viewport height (`SubdomainNavBar` fixed top belongs to marketing sites, not Product workspace chrome).
- [ ] **Target sizes `[Universal]`**: Interactive controls hit `>= 24x24px` (WCAG 2.2 SC 2.5.8 minimum) and ideally `>= 44x44px` on touch/mobile breakpoints (`narrow`).
- [ ] **Consistent grid/spacing rhythm `[Universal]`**: Spacing aligns to a consistent scale (4px/8px increments; no magic numbers like `17px`, `35px`); right/left margins step cleanly (`16px` mobile / `24px` desktop in Product; `16/32/48px` gutters in 12-col Brand grid).
- [ ] **Modal discipline `[Universal]`**: Forms inside dialogs switch cleanly to fullscreen on mobile (`narrow`); landscape phones (`height < 480px`) fall back to scrollable sheets; never stack `> 2` dialogs or deep-link dialogs via URLs; dialogs do not embed internal sidebar navigation.

Sources: `references/product/02-layout-spacing-responsive.md`, `references/product/06-navigation-and-ia.md`, `references/brand/02-brand-color-typography-and-grids.md`.

## 3. Accessibility and interaction integrity (WCAG 2.2 AA)

- [ ] **Semantic HTML before ARIA `[Universal]`**: Navigation uses `<a href>`; in-page state changes use `<button type="button">`; lists use `<ul>`/`<ol>`; headings follow strict `<h1> -> <h2> -> <h3>` outline without skipped levels. `role="application"` is absent unless truly building an unconstrained canvas/editor.
- [ ] **Visible `:focus-visible` indicator `[Universal]`**: Keyboard focus outline (`>= 2px`, `>= 3:1` contrast) is never removed (`outline: none` banned) and never clipped inside `overflow: hidden` or behind sticky headers (WCAG 2.2 SC 2.4.11 Focus Not Obscured).
- [ ] **Accessible names match visible labels `[Universal]`**: Voice-control users can activate controls via visible text (WCAG 2.5.3 Label in Name); icon-only buttons carry explicit `aria-label` + non-duplicative tooltip naming the action.
- [ ] **Discoverable inactive controls (`aria-disabled`) `[Primer]` / `[Universal]`**: Key unavailable buttons/tabs stay focusable with `aria-disabled="true"` plus visible explanation instead of native `disabled` that silently vanishes from Tab order.
- [ ] **No high-risk banned UI patterns `[Primer]` / `[Universal]`**:
  - **Zero Toast notifications**: Use persistent inline banners/messages + live-region announcements instead of disappearing snackbars/toasts.
  - **Zero tooltips on disabled/static text**: Tooltips only on interactive focusable triggers, never holding critical instructions or duplicate text.
  - **Zero hover-only critical controls**: Actions like Copy, Edit, Delete, or Row Actions remain keyboard-focusable and visible on touch devices.
  - **Zero truncation hiding primary identifiers**: Important names/errors wrap or allow clean expansion rather than ellipses without access.
- [ ] **Polite / single live-region feedback `[Universal]`**: Dynamic outcomes (Copy `"Copied!"`, async result loaded, step complete) announce cleanly once (`aria-live="polite"`; assertive reserved solely for critical errors), without interrupting navigation or wrapping whole containers in `aria-live`.
- [ ] **Shortcut safety (WCAG 2.1.4) `[Universal]`**: Single-character hotkeys can be remapped/disabled or require a focus/modifier scope, and never collide with screen-reader keys.
- [ ] **Reduced motion & auto-play control `[Universal]`**: Every transition/animation obeys `@media (prefers-reduced-motion: reduce)`; no unpausable animation lasts `> 5s`; max `<= 3` flashes/sec.

Sources: `references/product/05-accessibility.md`, `references/brand/03-brand-visuals-illustration-motion.md`.

## 4. Design tokens, color contrast, and CSS hygiene

- [ ] **Zero raw hex / color literals in semantic UI `[Primer]` / `[Universal]`**: Components consume semantic role variables (`--fgColor-*`, `--bgColor-*`, `--brand-color-*`, or project equivalents) rather than hardcoded hex/RGB values.
- [ ] **Contrast thresholds verified `[Universal]`**: Body text meets `>= 4.5:1` (`>= 3:1` for large text `>= 18pt` or `14pt bold`); input borders, focus rings, icons, and chart marks meet `>= 3:1`. High-contrast mode compatibility (`forced-colors: active` preserves borders/focus without invisible backgrounds) checked.
- [ ] **Banned color pairings absent `[Primer]`**: `--bgColor-muted` never holds `--fgColor-muted`; `--bgColor-*-emphasis` pairs exclusively with `--fgColor-onEmphasis`; Brand `Hero`/`CTABanner` muted descriptions never sit directly over busy imagery.
- [ ] **Not color alone `[Universal]`**: Status badges, diffs, required indicators, chart series, and validation states pair hue with text, distinct icon geometry (`Octicons` filled/outline differences), or marker/stroke patterns.
- [ ] **Zero deprecated styling APIs `[Primer]`**: `@primer/react` code uses CSS Modules + semantic CSS custom properties with **zero** `<Box>` / `sx` prop usage; `@primer/react` deprecated components (`CircleBadge`, `TextInputWithTokens`) avoided.
- [ ] **Paired shadow & z-index hygiene `[Universal]`**: Static UI uses flat `1px` borders; only floating menus (`dropdown`), backdrops (`overlay`), dialogs (`modal`), popovers (`popover`), and skip links (`skipLink`) climb elevation tiers—no arbitrary `z-index: 9999`.

Sources: `references/product/03-typography-and-visual-hierarchy.md`, `references/product/04-color-system-and-tokens.md`, `references/brand/02-brand-color-typography-and-grids.md`.

## 5. Typography, hierarchy, and UX copy

- [ ] **Comfortable prose measure & alignment `[Universal]`**: Multi-line prose caps at `<= 80ch` and stays start/left-aligned (no centered multi-line paragraphs or ragged `justified` columns).
- [ ] **Sensible scale & calibrated weights `[Universal]` / `[Brand]`**: Product UI keeps body `14px` (dense) or `16px` (reading), captions `12px` short labels only, `1.5` line height; Brand UI avoids uncalibrated `font-weight: 800`/`900`, serif fonts, multiline ALL-CAPS monospace, and ligatures in display headings.
- [ ] **Plain, action-oriented microcopy `[Universal]`**: ~7th-grade clarity; verbs match actual outcomes (*"Remove member"* vs *"Delete member"* accurately reflects non-destructive vs permanent outcomes); sentence case for buttons, headings, and labels; no `"Click here"`/`"Learn more"` naked links; no `"please"` filler bloat or `Lorem ipsum`.
- [ ] **Honest timestamps & tabular figures `[Primer]` / `[Universal]`**: Relative dates avoid ambiguous `micro` shorthand (`"1m"` sounds like meters) and add accessible context instead of `title`-only mouse tooltips; numeric table columns use `tabular-nums`.

Sources: `references/product/03-typography-and-visual-hierarchy.md`, `references/product/10-iconography-and-content-ux.md`, `references/brand/02-brand-color-typography-and-grids.md`.

## 6. Forms, saving, loading, errors, and data density

- [ ] **Persistent labels & group structure `[Universal]`**: Every input has a visible static `<label>` outside the box (never placeholder-as-label, never `maxlength` silent truncation, optional fields explicitly balanced with clear required markers). Single-column stacking used by default.
- [ ] **Validation timing & focus recovery `[Universal]`**: Errors validate cleanly after blur/submit (never while typing a fresh empty field); `< 3` errors focus the first invalid field, `>= 3` errors display a top Error Summary Banner with jump links and never wrap validation text inside `aria-live`.
- [ ] **Save vs immediate change separation `[Universal]`**: Explicit `Submit / Save / Cancel` forms stay strictly separate from immediate-effect controls (`ToggleSwitch`, instant filters); CTA alignment matches context (bottom-left on settings pages, bottom-right inside dialogs/comments).
- [ ] **Honest calibrated feedback `[Universal]`**: `< 1s` instantaneous, `1–3s` subtle indicator, `3–10s` delayed `Spinner` or skeleton matching final geometry (`aria-busy`, single announcement on completion), `> 10s` explicit progress + time/cancel guidance. Degraded views never display misleading `"No items"` empties or wipe global headers.
- [ ] **Destructive safety `[Primer]` / `[Universal]`**: Destructive confirmation dialogs (`296/320/480px`) default keyboard focus to **Cancel** (or require typing exact identifier for irreversible resource loss); reversible actions prefer **Undo** over naggy confirmation modals.
- [ ] **Clean tables & charts `[Universal]`**: Empty data cells remain cleanly blank (never `"-"`); chart bars (`<= 10`) and lines (`<= 5`) preserve `>= 3:1` mark contrast with outlines on muted fills and non-color differentiation.
- [ ] **Agentic / AI delegation transparency `[Universal]` / `[Primer]`**: Autonomous/Copilot tasks distinguish agent identity visually from humans, surface explicit review/approval gates, provide cancel/log access, and report outcomes truthfully.

Sources: `references/product/07-forms-feedback-and-onboarding.md`, `references/product/08-states-loading-saving-errors-data.md`, `references/product/09-scenario-patterns-and-components.md`.

## 7. Marketing / Brand craft and anti-AI-slop audit

- [ ] **Visual framing of product UI `[Brand]` / `[Universal]`**: Product screenshots in marketing pages are simplified/cropped around the key insight, wrapped in a neutral/dithered backdrop + matched contrast border (`never` naked `<img>`), and include thorough `alt` text covering visible code/controls.
- [ ] **Narrative rhythm & slot limits `[Brand]`**:
  - `Hero`: heading `<= 2` lines, description `3–4` lines, `<= 2` CTA actions in `ButtonGroup` (`1` primary per view).
  - `River`: `<= 160` characters per paragraph, `start`-aligned in gridline layouts (`never` zig-zagged), broken up by `Pillar`/`Card` or `RiverBreakout` (`never` first in stack).
  - `Card` vs `Pillar`: `Cards` used in interactive groups of `2–5` (`<= 3` centered, `4+` left-aligned); `Pillars` used in uniform image (`3–6`) or icon (`<= 9`) sets; matching image aspect ratios and headline lengths across siblings.
  - `FAQ`: short prose + lists only (`no` images inside answers).
  - `CTABanner` / `CTAForm`: placed to close the journey (`CTABanner balanced` includes required `Image`); `LogoSuite` shows `4–12` balanced monochrome logos; `Testimonial` keeps `<= 3–4` line quotes, real photos/company logos (`<= 4` per group).
- [ ] **Brand textures & mascots `[Brand]`**: Contribution graph & dithering never combined together; textures stay ambient on-grid (`never` scattered); mascots excluded from serious/billing/support/sales copy and deprecated Copilot heads/outline-octocats absent.
- [ ] **No deprecated Brand components `[Brand]`**: `Bento` (`use CTABanner / River`), `IDE`, `Button.hasArrow`, legacy `Hero` loose buttons, and legacy `River.Eyebrow` are absent.
- [ ] **Zero generic AI visual clichés `[Universal]`**: No purple/indigo gradient hero glows, no random `backdrop-filter: blur` frosted glass cards, no floating pill badges on every heading, no bouncing/slapping CSS entrance effects (`0.2–0.7s` controlled exponential ease-out only).

Sources: `primer/brand` (`packages/mcp/content/page-design.md`, `packages/mcp/src/review/rules.ts`), `references/brand/02..04`.

## Agent application notes

- **Output format when conducting a review**: Report **Blocking Defects** first (grouped with exact element/file/line and rule citation), followed by **Improvements / Polish**, and explicitly confirm **Not Applicable checks skipped** (e.g., `[GitHub-only]` rules skipped for a non-GitHub client) so evaluators know the difference between an intentional pass and a skipped scope rule.
- **Verify live, never assume**: If access to a browser/DOM/CSS inspector is available, verify computed `:focus-visible` outlines, `4.5:1` / `3:1` ratios across both light and dark themes, and `320px` horizontal overflow rather than trusting static JSX alone.

## Gaps, caveats and source issues

- **Automated vs human criteria**: Structural heuristics in `@primer/brand-mcp/src/review/rules.ts` catch JSX/CSS literal smells (hex values, weights `800`/`900`, backdrop blur, raw `<img>`, lorem ipsum), whereas human accessibility testing (real screen-reader navigation with NVDA/JAWS/VoiceOver, cognitive flow, genuine user comprehension) remains mandatory per `primer.style/accessibility/` and cannot be replaced cleanly by linters alone.
- **Unpublished thresholds**: Certain limits (such as Primer's ~15-item `ActionMenu` ceiling or ~20-row `DataTable` page default) are documented as design heuristics rather than hard API errors; treat them as strong review flags unless user domain requirements justify higher counts with virtualization or search/filtering.
