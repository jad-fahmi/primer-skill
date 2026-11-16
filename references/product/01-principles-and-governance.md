# Primer Product Design Philosophy, Governance, and Component API Principles

> Consolidates Primer's core product-UI philosophy, The Zen of GitHub, pattern governance, component lifecycle states, and React component API architecture for AI coding agents.

## Contents

- [Key takeaways](#key-takeaways)
- [Core product UX pillars](#core-product-ux-pillars)
- [The Zen of GitHub in UI engineering](#the-zen-of-github-in-ui-engineering)
- [Pattern governance and reuse discipline](#pattern-governance-and-reuse-discipline)
- [Component lifecycle and package status](#component-lifecycle-and-package-status)
- [Component API architecture and extension model](#component-api-architecture-and-extension-model)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Universal]` Product interfaces succeed when they reduce human-to-software friction through **cohesion, built-in accessibility, true responsiveness, and speed/flow** rather than retrofitting polish or accessibility after launch.
- `[Universal]` **The Zen of GitHub** establishes 14 operational tie-breakers (e.g., *"Responsive is better than fast"*, *"Anything added dilutes everything else"*, *"Design for failure"*) that resolve design ambiguity without committee bloat.
- `[Universal]` **Reuse before inventing**: evaluate existing patterns and components first; build locally only when the problem is genuinely specialized, and avoid duplicating existing component names or behaviors.
- `[Primer]` **Do not treat all Primer artifacts as equally production-safe**: check component status (`Experimental`, `Ready`, `Deprecated`), note that `Box` and the `sx` prop are deprecated in `@primer/react` in favor of CSS Modules, and note that `primer_view_components` (Rails) is in maintenance mode.
- `[Universal]` **Spectrum of Abstraction**: decompose UI engineering into Utilities/hooks -> Base unstyled accessibility primitives -> Presentational styled parts -> Config components, rather than building monolithic one-size-fits-all widgets.

## Core product UX pillars

Primer's product philosophy starts from the premise that productivity tools serve a global, diverse audience whose devices, connection speeds, and physical or cognitive abilities cannot be assumed.

1. **Cohesive mental models `[Universal]`**
   - **Rule**: Share interaction metaphors, vocabulary, and component roles across surfaces instead of reinventing UI per page.
   - **Why**: Familiar patterns let users complete tasks and discover features without re-learning new interfaces on every screen.
   - **When not to copy blindly**: Reuse *patterns* (consistent placement, predictable affordances), not GitHub's exact repo-centric chrome when building unrelated software.

2. **Accessibility from day zero `[Universal]`**
   - **Rule**: Bake semantic structure, keyboard behavior, contrast, and screen-reader support into the earliest design and component primitives.
   - **Why**: Primer explicitly warns that bolting accessibility onto a finished design becomes a costly, fragile retrofit because accessibility shapes layout, component state, and architecture.

3. **Device-respectful responsiveness `[Universal]`**
   - **Rule**: Adapt layout, navigation, and input affordances to viewport and user preferences without stripping desktop capability down to a single-column mobile caricature.
   - **Why**: Productivity software must stay fast and rich on large screens while remaining fully functional at narrow widths and high browser zoom.

4. **Efficiency, calm, and flow `[Universal]`**
   - **Rule**: Favor compact, calm, logical layouts over flashy decoration or modal interruptions.
   - **Why**: Productive work depends on uninterrupted focus; visual noise and unexpected blocks pull users out of flow.

5. **Common vocabulary between design and engineering `[Universal]`**
   - **Rule**: Standardize shared terminology (documented in both design and code) over hyper-specialized jargon that isolates disciplines; document both goals and explicit non-goals.
   - **Why**: Design systems act as a bridge between designers and engineers; shared names cut handoff friction and mismatched expectations.

Sources: [https://primer.style/product/getting-started/](https://primer.style/product/getting-started/); `primer/react` `contributor-docs/principles.md`; `primer/design` `content/guides/introduction.mdx` (archived).

## The Zen of GitHub in UI engineering

Originally distilled by Kyle Neath (2014, inspired by *The Zen of Python*) and codified in the GitHub API and Primer's introduction, these 14 aphorisms act as tie-breakers when product teams face design ambiguity. Below is each aphorism translated into concrete UI decisions:

| Aphorism | Operational UI Meaning | Tag |
| --- | --- | --- |
| *"Responsive is better than fast"* | Immediate UI acknowledgement (pending/optimistic states, instant interaction feedback) matters more to perceived speed than raw backend throughput that leaves the UI frozen. | `[Universal]` |
| *"It's not fully shipped until it's fast"* | Performance and low bundle/render cost are functional requirements, especially for low-powered devices and slow networks. | `[Universal]` |
| *"Anything added dilutes everything else"* | Every extra button, badge, border, or panel competes for attention and reduces the prominence of primary actions. Subtract before adding. | `[Universal]` |
| *"Practicality beats purity"* | Solve the user's actual task cleanly rather than forcing theoretical architecture or design dogma that slows delivery or hurts usability. | `[Universal]` |
| *"Approachable is better than simple"* | Do not hide essential controls just to make a screenshot look minimalist; preserve clarity and discoverability so newcomers and power users both succeed. | `[Universal]` |
| *"Mind your words, they are important"* | Microcopy (button verbs, error explanations, status names) directly governs usability; precise, unambiguous labels prevent user error. | `[Universal]` |
| *"Speak like a human"* | Write clear, conversational UI copy rather than robotic error codes or bureaucratic phrasing (unless legal/destructive precision demands formality). | `[Universal]` |
| *"Half measures are as bad as nothing at all"* | Ship complete workflows including empty, loading, error, keyboard, and responsive states—not happy-path-only screens. | `[Universal]` |
| *"Encourage flow"* | Minimize context switches, multi-step detours, and full-page reloads where inline edits or continuous workflows work better. | `[Universal]` |
| *"Non-blocking is better than blocking"* | Reserve modal dialogs and blocking confirmations for high-stakes or destructive moments; prefer inline feedback, undo, or non-blocking banners for routine states. | `[Universal]` |
| *"Favor focus over features"* | Center each screen on one primary job rather than cramming secondary workflows into side panels and toolbars. | `[Universal]` |
| *"Avoid administrative distraction"* | Do not burden end users with internal configuration toggles, metadata noise, or setup chores that the system can default intelligently. | `[Universal]` |
| *"Design for failure"* | First-class handling of network errors, partial outages, empty states, and invalid inputs is mandatory, not an edge-case afterthought. | `[Universal]` |
| *"Keep it logically awesome"* | Combine rigorous structural logic with thoughtful polish—never sacrifice logic for whimsy, or joy for sterile rigidity. | `[Universal]` |

**Why written principles matter `[Universal]`**: Neath and Ben Balter emphasize that unwritten "taste" decays into lowest-common-denominator compromises as teams scale. Putting principles in code/docs gives agents and teams explicit tie-breakers instead of guesswork.

Sources: [https://primer.style/product/getting-started/](https://primer.style/product/getting-started/); [https://warpspire.com/posts/taste](https://warpspire.com/posts/taste); [https://ben.balter.com/2015/08/12/the-zen-of-github/](https://ben.balter.com/2015/08/12/the-zen-of-github/).

## Pattern governance and reuse discipline

Primer does not require forcing every bespoke need into a shared component library. Instead, it prescribes a clear decision tree (`Handling new patterns` / `Adding new components`):

1. **Audit existing components and patterns first `[Universal]`**: Before creating custom UI, verify whether an existing system component solves the problem or can be extended safely.
2. **Know when NOT to generalize `[Universal]`**: Keep a component local to your feature/app when:
   - It solves a specialized domain workflow unlikely to apply elsewhere.
   - It embeds app-specific data fetching or API side effects (Primer components stay purely presentational).
   - It is exploratory or rushed with unresolved quality/accessibility gaps.
3. **Know when a pattern earns promotion `[Universal]`**: Propose/extract a shared component only when:
   - Multiple features/products need the same interaction.
   - It consolidates slightly different ad-hoc implementations solving the same problem.
   - Its boundaries, responsibilities, and documentation can be stated clearly without app-specific dependencies.
4. **Never shadow existing component names `[Universal]`**: Do not create a custom component with the same name as a design-system component if it behaves differently; evolve the upstream component or choose a distinct name.
5. **High entry bar protects consumers `[Primer]`**: Primer accepts a slower onboarding path for new shared components so teams consuming them can trust accessibility, theming, SSR, and responsive support by default.

Sources: [https://primer.style/product/contribute/handling-new-patterns/](https://primer.style/product/contribute/handling-new-patterns/); [https://primer.style/product/contribute/adding-new-components/](https://primer.style/product/contribute/adding-new-components/); [https://primer.style/product/contribute/design/](https://primer.style/product/contribute/design/).

## Component lifecycle and package status

AI coding agents inspecting Primer repos or docs must distinguish maturity levels and maintenance notices before adopting a component or package:

| Status (Live Docs) | Legacy Milestone Mapping | Agent Decision Rule | Tag |
| --- | --- | --- | --- |
| **Experimental** | Experimental (Draft / early Alpha) | Proof-of-concept or WIP; expect breaking changes and verify accessibility/docs before shipping. Prefer Ready alternatives when available. | `[Primer]` |
| **Ready** | Alpha -> Beta -> Stable (note: archived `component-lifecycle.mdx` treated Alpha as production-safe with breaking changes possible, Beta as multi-instance production-verified + SSR, Stable as frozen API + codemods/docs) | Default choice for production UI; backed by accessibility reviews, theming, tests, and major-versioned migration paths. | `[Primer]` |
| **Deprecated** | Deprecated -> Removed | Avoid in new code; emits dev warnings. Follow documented migration path to replacement component. | `[Primer]` |

**Critical current platform deprecations and maintenance states `[Primer]`**:
- **`Box` and `sx` prop in `@primer/react`**: Explicitly deprecated (`migration-from-box.md` and `@primer/mcp` `primer_coding_guidelines`). Do **not** generate `<Box>` or `sx={{...}}` in `@primer/react` code; render semantic HTML elements (`<div>`, `<section>`) styled with **CSS Modules + CSS custom property tokens + `clsx`**. *(Caveat: `@primer/react-brand` for marketing sites still documents its own `Box` layout component.)*
- **`primer_view_components` (Rails)**: Officially in **maintenance mode**; GitHub directs internal teams to migrate to Primer React and external consumers to fork if continuing use.
- **Drafts / Deprecated bundles**: New replacements incubate in `@primer/react/drafts`; retired v1 components move to `@primer/react/deprecated` for ~6 months before removal.

Sources: [https://primer.style/product/getting-started/component-status/](https://primer.style/product/getting-started/component-status/); [https://primer.style/product/getting-started/rails/](https://primer.style/product/getting-started/rails/); `primer/react` `contributor-docs/deprecating-components.md`, `contributor-docs/migration-from-box.md`, `packages/mcp/src/server.ts`; `primer/design` `content/guides/component-lifecycle.mdx` (archived).

## Component API architecture and extension model

Primer's engineering documentation offers broadly applicable React/UI architecture rules (`style.md`, `extending-components`, `modular-ds-spectrum-model`, `component-contents-api-patterns.md`):

### 1. Spectrum of Abstraction `[Universal]`
Structure component libraries into four intentional layers (not every component needs all four):
- **Utilities / Hooks**: Reusable state, focus, keyboard, or positioning logic (e.g., `useControllableState`, `useFocusTrap`, `useFocusZone`, `useOnEscapePress`, `@primer/behaviors`).
- **Base components**: Unstyled primitives owning ARIA wiring, keyboard semantics, and reset styles (e.g., `ButtonBase`) so teams don't reinvent native interactive behavior.
- **Presentational components**: Styled, composable building blocks (`ActionList`, `Button`) where the consumer controls ordering and conditional rendering while the system owns tokens and ARIA contracts.
- **Config components**: Opinionated, props/data-driven facades for high-frequency standard patterns. When a consumer outgrows config props, they drop down to presentational pieces + hooks instead of bloating the config API with flags.

### 2. Choosing Children vs. Data Contracts `[Universal]`
- **Pass React `children` (composition)** when child structure varies freely, mirrors HTML containment, or the parent simply wraps/styles arbitrary contents without peeking inside (`React.Children` inspection and `React.cloneElement` are discouraged because they violate element-owner authority).
- **Pass structured data props** (with optional render-prop escape hatches that retain sensible defaults) when data must be transformed/validated before rendering, or when strict structural uniformity is required.
- **Avoid hybrid APIs** that mix both data props and arbitrary `children` for the same list, because ordering and interaction become unpredictable.

### 3. Safe Extension Patterns in `@primer/react` `[Primer]` / `[Universal]`
1. **`as` prop (polymorphism)**: Change the rendered HTML/router element (`<Button as="a" href="...">`) without losing visual styling or behavior. Do not use `as` to inject positioned sub-parts.
2. **Named Slots (`useSlots` + `__SLOT__`)**: Compound parents (`ActionList.Item`, `FormControl`, `Dialog`, `PageLayout`, `PageHeader`) identify positioned children by direct reference or `__SLOT__` symbol.
   - *Gotcha*: Wrapping a slot component without copying `Wrapper.__SLOT__ = Original.__SLOT__` silently drops it into default `rest` content (caught by `eslint-plugin-primer-react` `primer-react/direct-slot-children`). Duplicate slots trigger a dev warning and first-match-wins. Do not introduce slots by default when ordinary JSX composition suffices.
3. **Wrapping**: Preserves defaults or domain context, but polymorphic wrappers must re-declare `PolymorphicForwardRefComponent` or TypeScript loses merged element props.

### 4. Practical Implementation Hygiene `[Universal]` / `[Primer]`
- **SSR first**: Never call browser-only globals during initial render; avoid layout shifts on hydration.
- **Focus in event handlers, not `useEffect`**: Trigger focus moves inside user-interaction handlers so unrelated effect re-runs never hijack focus. (For visual positioning measurements that would flicker, `useLayoutEffect` is preferred over `useEffect`.)
- **Ref-accepting hooks & stable callbacks**: Hooks should *accept* a caller's `ref` (letting the caller compose multiple hooks on one element) rather than returning one, and store callbacks in refs instead of forcing unstable dependency arrays.
- **Controlled + Uncontrolled via `useControllableState`**, extendable object-parameter callbacks (`onChange({value})`), rest-prop forwarding to the root node with intentional `mergeProps`, `clsx` for conditional classes, `data-*` attributes for component states/variants (instead of variant class concatenation), and CSS Custom Properties to pass dynamic JS values into CSS Modules instead of inline style overrides.
- **Assume rule-breaking**: Design safe extension hatches (`className`, custom slots) rather than rigid lock-ins that force copy-paste forks.

Sources: [https://primer.style/product/getting-started/react/philosophy/](https://primer.style/product/getting-started/react/philosophy/); [https://primer.style/product/getting-started/react/extending-components/](https://primer.style/product/getting-started/react/extending-components/); [https://primer.style/product/getting-started/react/recommended-practices/](https://primer.style/product/getting-started/react/recommended-practices/); `primer/react` `contributor-docs/style.md`, `contributor-docs/component-contents-api-patterns.md`, `.agents/skills/modular-ds-spectrum-model/SKILL.md`.

## Agent application notes

- **Non-GitHub apps**: Adopt the 4 UX pillars, Zen of GitHub decision heuristics, Spectrum of Abstraction, and React engineering rules wholesale regardless of framework (Vue, Svelte, plain HTML/CSS, Tailwind, Radix/Ark). Only adopt `@primer/react` imports, `__SLOT__` conventions, and Primer token names when the project actually uses Primer.
- **Handling feature requests**: Before inventing a new UI pattern or heavy custom component for a user prompt, pause and ask: (1) does the project already have a component/pattern for this? (2) can existing parts compose it? (3) is this genuinely one-off or reusable? Keep local one-offs clean and self-contained rather than prematurely abstracting a fragile "system" component.
- **Avoiding overengineering**: Apply *"Anything added dilutes everything else"* and *"Engineer just enough of a solution, value declarative over abstraction"* (`principles.md`)—ship complete error/loading/empty states, not bloated configuration flags or decorative clutter.

## Gaps, caveats and source issues

- **Archived vs. live docs drift**: `primer/design` was archived on 2025-07-02 and replaced by a newer Next.js site at `https://primer.style/product/` (whose source repository is not public). Live docs simplify component statuses to `Experimental`, `Ready`, and `Deprecated`, whereas the archived `component-lifecycle.mdx` defined `Experimental -> Alpha -> Beta -> Stable -> Deprecated -> Removed`.
- **Internal-only links**: Several referenced governance docs—such as the onboarding course (`github/primer-onboarding`), internal backlog project boards, `ui/packages/` shared-components Storybook, and `github/primer` pattern proposal templates—are restricted to GitHub staff (`GitHub staff only`).
- **Box/sx discrepancy across ecosystems**: While `@primer/react` explicitly deprecates `Box` and `sx` in favor of CSS Modules, `@primer/react-brand` (marketing UI) still documents `Box`. Agents must verify which package a codebase uses before recommending or removing `Box`.
