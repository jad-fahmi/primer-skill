# Primer & GitHub Brand Design Skill (`primer-skill`)

An Agent Skill that distills [Primer](https://primer.style/) (Product UI, Accessibility, Primitives, and Octicons) and the [GitHub Brand Toolkit](https://brand.github.com/) into modular, actionable guidelines for AI coding agents building or reviewing web applications, design systems, and marketing websites.

> **Core philosophy**: Teach coding agents *why* mature design systems make structural, token, layout, accessibility, and storytelling decisions—**without** turning unrelated products into GitHub clones or forcing `@primer/react` / `@primer/react-brand` dependencies into Tailwind, shadcn/Radix, MUI, or custom stacks.

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

---

## Why Use This Skill?

AI coding agents commonly fail at UI work in predictable ways: conflating dense app interfaces with marketing hero templates, picking arbitrary colors or muted-on-muted low-contrast text, relying on hover-only controls or disappearing toasts, cluttering cards with purple gradients and `800`/`900` weights, or cloning one brand's identity everywhere.

`primer-skill` solves this by:

- **Separating Product UI from Brand / Marketing UI** cleanly across tokens, typography, motion budgets, spacing, and narrative patterns.
- **Tagging every rule by portability** (`[Universal]` vs `[Primer]` / `[Brand]`) so agents extract durable UX craft for *any* framework (Tailwind, Radix/shadcn, plain CSS Custom Properties, MUI, Vue, Rails) while keeping GitHub-specific APIs and marks strictly scoped to GitHub projects.
- **Preventing stale/deprecated API usage** by documenting real upstream deprecations (`Box`/`sx` in `@primer/react`, `Bento` and `IDE` in `@primer/react-brand`, maintenance-mode `primer_view_components`, archived `primer/design`, and 2025/2026 retired Copilot branding marks).
- **Shipping a concrete Design Review Checklist** to catch WCAG 2.2 AA violations, broken validation/loading/error flows, and generic AI visual clichés before code lands.

---

## Quick Start & Installation

Install into any Agent Skill–compatible tool (Antigravity, Claude Code, Codex, Cursor, VS Code Copilot, or `skills` CLI workflows):

### Option 1: Skills CLI (`npx skills`)

```bash
npx skills add jad-fahmi/primer-skill
```

### Option 2: Clone directly into your workspace or global skills folder

Project-scoped (shared via your repository):

```bash
# Universal cross-agent skills directory (Antigravity / Codex / Cursor / etc.)
git clone https://github.com/jad-fahmi/primer-skill.git .agents/skills/primer-skill

# Or Claude Code project skills
git clone https://github.com/jad-fahmi/primer-skill.git .claude/skills/primer-skill

# Or Antigravity project skills
git clone https://github.com/jad-fahmi/primer-skill.git .gemini/skills/primer-skill
```

User-global (available across all projects):

```bash
# Antigravity global skills
git clone https://github.com/jad-fahmi/primer-skill.git ~/.gemini/antigravity/skills/primer-skill

# Claude Code global skills
git clone https://github.com/jad-fahmi/primer-skill.git ~/.claude/skills/primer-skill
```

Once installed, agents discover [`SKILL.md`](SKILL.md) automatically and load only the specific files under [`references/`](references/) relevant to the task.

---

## Example Prompts That Benefit From This Skill

- *"Build a calm, accessible B2B SaaS billing & audit dashboard in Tailwind + React using semantic color tokens and honest empty/loading/error states—without looking like GitHub."* -> Loads [`cross/01`](references/cross/01-product-vs-brand-and-adaptation.md), [`product/04`](references/product/04-color-system-and-tokens.md), [`product/05`](references/product/05-accessibility.md), and [`product/08`](references/product/08-states-loading-saving-errors-data.md).
- *"Design a crisp developer-tool launch page in vanilla CSS Modules with optical display weights, framed UI screenshots, and zero generic AI glow clichés."* -> Loads [`brand/02`](references/brand/02-brand-color-typography-and-grids.md), [`brand/03`](references/brand/03-brand-visuals-illustration-motion.md), and [`cross/01`](references/cross/01-product-vs-brand-and-adaptation.md).
- *"Add a safe multi-item delete flow and filterable table using `@primer/react` and `@primer/primitives` CSS Modules without deprecated `Box` or `sx` props."* -> Loads [`product/01`](references/product/01-principles-and-governance.md), [`product/07`](references/product/07-forms-feedback-and-onboarding.md), and [`product/09`](references/product/09-scenario-patterns-and-components.md).
- *"Review this pull request's UI components, forms, and marketing hero against WCAG 2.2 AA and modern design-system rules."* -> Executes [`cross/02-design-review-checklist.md`](references/cross/02-design-review-checklist.md).

---

## What's Inside (`SKILL.md` + 17 Reference Modules)

[`SKILL.md`](SKILL.md) stays compact (72 lines) and routes agents progressively into three modular reference groups:

### Product UI, Accessibility & Octicons ([`references/product/`](references/product/))

| Module | Covers |
| --- | --- |
| [01-principles-and-governance.md](references/product/01-principles-and-governance.md) | Zen of GitHub, 5 UX pillars, pattern promotion rules, component maturity, `Box`/`sx` deprecation, `primer_view_components` status, Spectrum of Abstraction |
| [02-layout-spacing-responsive.md](references/product/02-layout-spacing-responsive.md) | `narrow`/`regular`/`wide` viewport ranges, `320px–1400px` breakpoints, `PageLayout`/`SplitPageLayout`, spacing & control scales, `320x256` reflow, `24px`/`44px` targets |
| [03-typography-and-visual-hierarchy.md](references/product/03-typography-and-visual-hierarchy.md) | Product font stacks, `--text-*-shorthand*` scale, `<h1>–<h6>` semantic decoupling, `<=80ch` line measure, borders, shadows, `--zIndex-*`, `<=300ms` UI motion |
| [04-color-system-and-tokens.md](references/product/04-color-system-and-tokens.md) | W3C JSON5 3-tier tokens, Primitives ADRs `000–011`, 14-step neutral zones, 12 semantic roles, themes, AA/`G174`/`forced-colors`, mandatory pairings |
| [05-accessibility.md](references/product/05-accessibility.md) | WCAG 2.2 AA, 4x3 disability matrix, First Rule of ARIA, `aria-disabled`, `@primer/behaviors`, `@primer/live-region-element`, Toast/hover/tooltip bans, 16-component ARIA matrix, Copilot AI & CLI accessibility |
| [06-navigation-and-ia.md](references/product/06-navigation-and-ia.md) | Transition types, nav selector (`NavList`, `TreeView`, `UnderlineNav`, `UnderlinePanels`, `SegmentedControl`, `Breadcrumbs`, `PageHeader`, `ActionMenu`), responsive sidebars, `Dialog` & sheet geometry |
| [07-forms-feedback-and-onboarding.md](references/product/07-forms-feedback-and-onboarding.md) | `FormControl` anatomy, input pickers, validation timing & `<3` vs `>=3` error focus, `ConfirmationDialog` danger focus, 6 banner tiers, `Popover`/`Details` icons, timeboxed onboarding & preview labels |
| [08-states-loading-saving-errors-data.md](references/product/08-states-loading-saving-errors-data.md) | `<1s`/`1–3s`/`3–10s`/`>10s` loading delays, explicit-vs-auto save isolation, `Blankslate`, partial outage triage, chart density/contrast, `DataTable` (`tabular-nums`, blank empty cells) |
| [09-scenario-patterns-and-components.md](references/product/09-scenario-patterns-and-components.md) | All 9 Scenario Patterns (`Copy`, `Create`, `Customize`, `Delegate`, `Delete`, `Edit`, `Filter`, `Search`, `View`), 62 live Product component statuses, 25 Internal Components boundary, migrations |
| [10-iconography-and-content-ux.md](references/product/10-iconography-and-content-ux.md) | Octicons `12/16/24px` optical rules & accessible SVGs, docs illustration shapes, 7th-grade UI copy guidelines, `RelativeTime` formatting, archived `primer.style/native` (`gh`, Desktop, Mobile) |

### Brand Identity & Marketing ([`references/brand/`](references/brand/))

| Module | Covers |
| --- | --- |
| [01-brand-identity-and-voice.md](references/brand/01-brand-identity-and-voice.md) | Brand vs product positioning, 5 Brand Attributes (`Nerdy`, `Confident`, `Authentic`, `Imaginative`, `Empathetic`), public voice bounds, logo lockups, 2025/2026 Copilot identity retirements, co-branding & trademarks |
| [02-brand-color-typography-and-grids.md](references/brand/02-brand-color-typography-and-grids.md) | GitHub Green (`#0FBF3E`), HEX/RGB/CMYK/PMS Brand Toolkit swatches vs `@primer/brand-primitives` `--brand-*` tokens, `Mona Sans` `400–550` variable weights, `14px–64px` scale, 12-col Grid/Gridline/Flexsuite, AI-slop tells |
| [03-brand-visuals-illustration-motion.md](references/brand/03-brand-visuals-illustration-motion.md) | Expression spectrum, 3 illustration pillars, contribution graph vs dithering rules, marketing UI screenshot framing, Mona/Ducky limits, Spot icons, diagrams, Brand Motion (`0.2–0.7s`, `AnimationProvider`, 5 patterns) |
| [04-primer-brand-components-and-in-action.md](references/brand/04-primer-brand-components-and-in-action.md) | `@primer/react-brand` setup & `@primer/brand-mcp`, 55+ components & 9 forms (flagging deprecated `Bento`/`IDE` & `RiverStoryScroll` MDX gap), `Card` vs `Pillar` sizing, channel rules (Web, Social, Experiential, eBooks, Presentations, Swag) |

### Cross-Cutting Guides, Review & Traceability ([`references/cross/`](references/cross/))

| Module | Covers |
| --- | --- |
| [01-product-vs-brand-and-adaptation.md](references/cross/01-product-vs-brand-and-adaptation.md) | Mode decision matrix, `@primer/react` vs `@primer/react-brand` comparison, `[Universal]` distillation, adaptation recipes for Tailwind, shadcn/Radix, plain CSS, MUI, and server-rendered apps |
| [02-design-review-checklist.md](references/cross/02-design-review-checklist.md) | 7-part pass/fail audit checklist spanning stack fit, layout/reflow, WCAG 2.2 AA, token pairings, UX microcopy, forms/states/tables, and marketing/anti-AI-slop rules |
| [03-sources-licensing-and-gaps.md](references/cross/03-sources-licensing-and-gaps.md) | Verified ledger of 415 `primer.style` pages, 32 `brand.github.com` pages + 89-page PDF, 11 GitHub repos, MIT / SIL OFL 1.1 / trademark rules, staff-only gaps, and doc-vs-code discrepancies |

---

## Research Integrity & Limitations

Every claim traces to primary sources inspected in November 2026 (`415` live `primer.style` pages, `32` live `brand.github.com` HTML/Markdown pages, `GitHub-Brand-Guidelines-2026.pdf`, and 11 cloned official repositories). Staff-only internal resources (`github/brand.github.com`, `github/primer-docs`, `how-to-write-at-github.md`, `github/accessibility`, `github/scenarios`, `github/github-ui`, Figma kits, and presentation decks) and outlined vector PDF spreads remain inaccessible outside GitHub and are explicitly flagged in [`references/cross/03-sources-licensing-and-gaps.md`](references/cross/03-sources-licensing-and-gaps.md) rather than guessed.

---

## License & Legal Notice

- **This skill repository**: Licensed under the [MIT License](LICENSE).
- **Independence disclaimer**: `primer-skill` is an independent community educational synthesis built from public documentation and MIT-licensed Primer repositories. It is **not** an official GitHub product and is not affiliated with, sponsored by, or endorsed by GitHub, Inc.
- **Trademarks & Proprietary Brand Assets**: Upstream Primer code/docs carry MIT licenses and `Mona Sans` carries the SIL Open Font License v1.1, whereas `GITHUB®`, `INVERTOCAT`, `OCTOCAT®`, mascots, and downloadable GitHub Brand Guidelines remain registered trademarks and copyrighted property of GitHub, Inc. Follow [GitHub's logo & trademark rules](references/brand/01-brand-identity-and-voice.md#trademark-and-copyright-guardrails) before referencing GitHub marks in external projects.
