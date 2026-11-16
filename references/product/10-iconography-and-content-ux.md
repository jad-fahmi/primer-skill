# Iconography, UX Content, Time Formatting, and Native Platform Guidance

> Consolidates Primer's Octicons visual geometry and accessible markup rules, illustration vs. brand-trademark boundaries, product UX writing & `RelativeTime` formatting standards, and archived native platform (`CLI`, `Desktop`, `Mobile`) design foundations.

## Contents

- [Key takeaways](#key-takeaways)
- [Octicons visual architecture, naming, and SVG markup](#octicons-visual-architecture-naming-and-svg-markup)
- [Product illustrations vs. GitHub brand trademarks](#product-illustrations-vs-github-brand-trademarks)
- [Product UX writing and microcopy guidelines](#product-ux-writing-and-microcopy-guidelines)
- [Dates, durations, and RelativeTime formatting](#dates-durations-and-relativetime-formatting)
- [Native platforms: CLI, Desktop, and Mobile notes](#native-platforms-cli-desktop-and-mobile-notes)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Primer]` **Octicons enforce strict optical harmony**: Icons ship in **`16×16`** and **`24×24`** grids (with rare **`12px`** exceptions), **`1.5px` stroke weight**, round caps/joins, outer-edge pixel alignment, and automatic decorative-by-default semantics (`aria-hidden="true" focusable="false"` unless given an `aria-label`).
- `[Universal]` **Write UI copy like clear software documentation**: Target a **7th-grade reading level or below**, use sentence case everywhere (never trailing periods/colons on labels or buttons), avoid apologies (`"Sorry"`), humor (`"Oops"`), blame (`"You forgot"`), and condescension (`"easy"`, `"just"`), and always reuse trigger verbs inside status feedback.
- `[Universal]` **Prefer full readable timestamps over micro abbreviations**: Avoid shorthand like `2mo` or `1m` (breaks machine translation and VoiceOver reads `1m` as `"1 meter"`); never rely solely on HTML `title` tooltips to expose exact dates.
- `[Primer]` **Respect ecosystem & legal boundaries**: Do not ship GitHub trademarks (`Invertocat`, `Octocat`, `Octovisuals`) as generic app chrome, and note that `primer.style/native` was retired in 2025 (its live successors for CLI accessibility now live in `primer.style/accessibility/foundations/cli-accessibility/`).

## Octicons visual architecture, naming, and SVG markup

### Optical construction rules `[Primer]`

Handcrafted to maintain uniform visual weight (*optical volume*) at small UI sizes:

- **Canonical grid sizes**: Author **`16×16`** and **`24×24`** optical masters for every icon; **`12px`** variants (e.g., compact alerts/checks inside 12px inline messages) are rare exceptions only where `16px` cannot fit. (React `<Octicon>` defaults to **`16px`**; Rails `Primer::Beta::Octicon` sizes `:xsmall` [`12`], `:small` [`16` default], `:medium` [`24`]).
- **Stroke & geometry**: Uniform **`1.5px`** stroke across both `16px` and `24px` masters; **round caps and round joins**. Snap outer shape edges (not inner strokes) cleanly to pixel grid lines.
- **Corner radii**: **`1px`** radius on standard corners (override only when shape clarity requires, e.g., `repo`); **`0.25px`** subtle rounding on tiny interior filled arrowheads/details.
- **Spacing & separation**: **`1px`** gap between overlapping shapes; **`1.5px`** gap cutting around overlaid modifier strokes (slashes, badges, dependency arrows).
- **Perspective & arrows**: Flat **2D** front-facing geometry by default (add isometric/depth only when an object is otherwise unrecognizable); **line arrowheads** default (`sign-out`), reserving **filled arrowheads** for tight glyphs (`link-external`).

### Naming, compatibility aliases, and package code `[Primer]`

- **Canonical naming**: Lowercase hyphenated descriptors (`issue-opened`, `git-pull-request-unlisted`), family prefixes, unsuffixed base Figma frame for 16px plus single numeric suffix for larger sizes (`issue-opened-24`). Preserve `aliasOf` compatibility names cleanly without breaking legacy lookups (`bookmark-fill` aliased from deprecated `bookmark-filled`, `repo-delete` from `repo-deleted`, `triangle-circle` retaining dual-drawing `play` compatibility).
- **Theme-reactive fills**: Icons inherit `currentColor` cleanly (`fill="currentColor"`), automatically tracking `--fgColor-default` or container color across light/dark/high-contrast modes—never hardcode static hex colors on SVGs. Use `verticalAlign` (`middle`, `text-bottom`, `text-top`, `top`) to align alongside type.
- **Accessible SVG markup (`foundations/icons/` & `octicons/code/`)**:
  - **Decorative (default beside text)**: `<svg aria-hidden="true" focusable="false">`. (`@primer/octicons-react` and Rails `Octicon` apply `aria-hidden="true"` automatically whenever `aria-label` is omitted.)
  - **Standalone contentful graphic**: `<Octicon icon={...} aria-label="Build failed" />` -> exposes `role="img"` + `aria-label` (or `<svg role="img" focusable="false"><title>...</title>`); do **not** allow `tabIndex` on static icon SVGs. Avoid using bare icons inside buttons/links without accessible control names (`IconButton` wraps `<button aria-label="...">` or visible `<Tooltip>`).

Sources: `primer/design` `content/foundations/icons/design-guidelines.mdx`; `primer/octicons` `README.md`, `docs/add-octicon-checklist.md`; `https://primer.style/octicons/code/`, `.../product/getting-started/foundations/icons/`.

## Product illustrations vs. GitHub brand trademarks

### Primer documentation / product illustration kit `[Primer]`

For subtle engineering/system illustrations (`primer-style/docs/illustration-guidelines.md`), combine a shared modular vocabulary bridging design and code:
- **Paintbrush**: Symbolic artistic motif—strictly **1 max per illustration**.
- **Squiggles**: `2px` (`blue-400`) or `1px` (`blue-200`) strokes, typically `40×8px` or `20×4px`, angled `0°`, `45°`, or `90°`.
- **Dots**: Uniform dot-grid backdrop shadow (`blue-200` on light canvas, `blue-700` on dark canvas).
- **Geometric shapes**: Unfilled stroke-only **Circles**, **Squares** (also rotated `45°` as diamonds), **Triangles**, and **Hexagons**—pair contrasting stroke weights (`2px` `blue-400` vs `1px` `blue-200`) whenever shapes sit adjacent.
- **Code Lines**: Horizontal block strips aligned to an **`8px` width scale**, heights limited to **`16px`**, **`24px`**, or **`32px`**, with equal **`8px`-scale** gaps cleanly on both sides.

### Brand marks, `Octovisuals`, and trademark guardrails `[Brand]` / `[Universal]`

- **`Octovisuals` restriction**: Internal GitHub marketing/illustration art (`octovisuals.github.com`) is private to GitHub staff. Public downloads (`GitHub-Logo.zip`, `github-mark.zip`) cover solely permitted linking, *"hosted/integrated with GitHub"* callouts, and press references—presented **less prominently than your own product's brand**.
- **Legal boundaries**: `GITHUB®`, `OCTOCAT®`, and the `INVERTOCAT` design are registered trademarks/copyrights of GitHub, Inc. Never modify colors/proportions, merge GitHub logos into your product icon/domain/company brand, or imply endorsement. Build distinct visual metaphors when authoring non-GitHub applications.

Sources: `primer/primer-style` `docs/illustration-guidelines.md`; `primer/design` `content/foundations/icons/octovisuals.mdx`.

## Product UX writing and microcopy guidelines

### Voice, clarity, and formatting rules `[Universal]` / `[Primer]`

GitHub product UI copy writes concisely at a **7th-grade reading level or below** (*Grammarly*, *Hemingway*) across four consistent traits: **Clear (not cold)**, **Conversational (not jargon-y)**, **Inclusive (not disingenuous)**, and **Helpful (not overly prescriptive)**—maintaining an informal, positive baseline while dropping humor cleanly in tense states (errors, waits, outages).

| Category | Rule | Good Example | Avoid |
| --- | --- | --- | --- |
| **Capitalization & punctuation** | **Sentence case everywhere** (forms, buttons, headings, empty states); **zero periods/colons** on labels, headings, or buttons; capitalize only proper nouns/product names (*GitHub*). Sentence starting with a numeral keeps following words lowercase. | `Save email preferences` / `2 references` | `Save Email Preferences.` / `Label:` / `2 References` |
| **CTAs & action labels** | Start with **imperative active verbs** or short Adj+Noun pairs. Use **`sign in`** over `"log in"`. Match exact terms across trigger and status confirmation. | `Save preferences` / `New issue` / *"Issue transfer in progress"* | `Done` / `Click here` / *"Moving the issue"* (after clicking *Transfer*) |
| **Links** | Unique descriptive destination text (never `"here"` / `"click here"`). If card/teaser uses image + text in one `<a>`, set decorative image `alt=""`. | *"details on our `<a href>GitHub Universe site</a>`"* | *"Click `<a href>here</a>` to learn more"* |
| **Error messages** | Human-readable, specific, zero apologies, zero humor, zero user blame. Name constraint/recovery cleanly. | `Your name must be between 2 and 20 characters` / `All checks failed` | `Error 123456` / `You forgot your name` / `Sorry, checks failed` / `Oops!` |
| **Person / Point of view** | Second person (`you`/`your`) by default; use first person (`I`) strictly for explicit user attestations/destructive consent checkboxes. | `Update your profile` / `I have read the Terms of Service` | `Update my profile` |
| **UI cross-references** | Unclickable UI regions in **`"quotes"`**; clickable buttons/links in **`**bold**`** (`tap` on mobile, `click` on desktop); directories/code in **`<code>`**. | Go to `"Email notifications settings"` and click **Save**. | *Open email settings and hit save!* |
| **Words & symbols to ban** | Never abbreviate **`pull request`** (lowercase mid-sentence); write `and` instead of `&`/`+`; omit `;` semicolons and `<`/`>` breadcrumbs; ban patronizing softeners (`easy`, `quick`, `just`); lowercase `am`/`pm`; **ban emojis in core UI copy** (if ever allowed in social/onboarding popovers, place at sentence end only, 1 max, well-known, light/dark legible, never replacing words). | `4:30 pm`, `Issues and pull requests`, `Save changes` | `Just click Save!`, `1 PR`, `4:30 P.M.`, `Issues + PRs`, `Step 1 > Step 2` |

Sources: `primer/design` `content/foundations/content.mdx`; `https://primer.style/product/ui-patterns/{forms,notification-messaging,empty-states}/`.

## Dates, durations, and RelativeTime formatting

Derived from `RelativeTime` (`relative-time-element`) guidelines (`product/components/relative-time/`):

| Context | Precision / Cutoff | Standard Output Format | Accessibility & Caveat Rules |
| --- | --- | --- | --- |
| **Past relative** | `< 1m` / `< 1 month` / `> 1 month` / past year | `just now` -> `5 minutes ago`, `yesterday`, `20 days ago` -> `on Nov 18` -> localized `on Nov 18, 2021` (order tracks locale e.g. US vs UK vs Korean). | Default prefix `"on"` customizable (`prefix=""`); expand month/weekday names cleanly where clarity matters. |
| **Future relative** | `< 1m` / `< 1 month` / `> 1 month` / next year | `just now` -> `in 2 minutes`, `tomorrow`, `in 14 days` -> `on Nov 18` -> localized `on Aug 26, 2025`. | Do **not** use vague relative phrases when users need an exact scheduled cutoff or past audit instant—switch to **Precise Dates**. |
| **Precise timestamp** | Exact scheduling / audit logs | Localized `weekday, MMM D, YYYY, HH:MM:SS [AP]M` (optional seconds/leading zeros). | Always expose exact localized time cleanly without mouse hover assumptions. |
| **Elapsed / Remaining** | Active CI runs / jobs | Long: `X years, X months...`; Compact (`second` / `day` precision): `5m 32s`, `1d 4h 32s`, `2y 8m 5d` (omit zero units). | Keep live status readable for long-running builds without rapid noisy announcements. |
| **Anti-micro rule** | Mobile / narrow columns | Use readable long words (`2 months ago`), **avoid `micro` (`2mo`, `1m`)**. | **Reason**: `micro` tokens fail browser machine translation and screen readers mispronounce them (**VoiceOver macOS reads `"1m"` as `"1 meter"`**). |

- **`<relative-time>` Accessibility Warning (`relative-time/accessibility/`) `[Primer]`**: `<relative-time>` exposes its exact localized date via a native HTML `title` attribute, which **cannot be reached with keyboard navigation and is ignored by screen readers on neutral custom elements**. Fix by wrapping interactive link timestamps in an accessible Primer **`Tooltip`** (hiding raw `title`) OR exposing full date strings inline / inside detail modals / via user preference toggles.

Sources: `https://primer.style/product/components/relative-time/{guidelines,accessibility}/`.

## Native platforms: CLI, Desktop, and Mobile notes

Notice that **`https://primer.style/native/` was deprecated and redirects to `/`**, while its documentation lives in archived `primer/design` (`content/native/`) and CLI accessibility moved directly into live `https://primer.style/accessibility/foundations/cli-accessibility/`:

- **GitHub CLI (`gh`) `[Primer]`**:
  - **Command grammar**: `<binary> <command-noun> <subcommand-verb> [arg] [--flags value]`. Keep flag modifiers as `--flags` rather than extra positional commands; pair verbose long flags (`--state`) with chainable short letters (`-s`); use dash-case placeholders (`<issue-number>`), `<required>`, `[optional]`, `{optA | optB}` in help pages (5 required sections: Usage, Core commands, Flags, Learn more, Inherited flags).
  - **Terminal UX**: Bias toward keyboard flow + prompt flags (`--title`, `E` opens `$EDITOR`, Enter skips/accepts CAPS-marked default `[Y/n]`), provide quick browser escapes (`--web`), output actionable entity URLs at completion, use bold/spacing hierarchy (**no italics** in terminal type), and punctuate lists/items cleanly with `.`, `,`, `:` for screen-reader pauses.
  - **ANSI color, symbols & pipes**: Use only the 8 standard ANSI hues to *enhance* meaning (`✓` Success, `-` Neutral, `✗` Failure, `+` Changes requested, `!` Alert—mind poor Unicode coverage in legacy fonts like Windows Lucida Console). Keep **piped/machine output** completely free of ANSI escapes, headers, truncation, or ambiguous implicit color—emit exact ISO/numeric dates and tab-delimited columns (`cut` compatible), respecting `NO_COLOR` and `$EDITOR`.
- **GitHub Desktop (Electron) `[Primer]`**:
  - **Neutral cross-OS adaptation**: Intentionally diverges from web GitHub UI to feel native—uses a **`10px` spacing scale** (vs web `8px`), **blue** primary buttons & tab underlines (vs web green/coral), and `text-x-small` (`11pt`) additions cleanly tuned for `Segoe UI` (Windows reads smaller than macOS `SF Pro`, so compensate scale thoughtfully).
  - **macOS vs. Windows UI divergences**: **macOS**: right-aligned primary CTA (`Cancel | Save`), Title Case headlines/buttons, OS-top menu bar; **Windows**: left-aligned primary CTA (`Save | Cancel`), Sentence case headlines/buttons, integrated in-window menu bar. Target clarity for beginners (*git vs. magic* balance, *"what's next"* visibility, translucent/invert-friendly 1–2-swatch welcome illustrations).
- **Native Mobile (`iOS`, `iPadOS`, `Android`) `[Primer]`**:
  - **OS-native first**: Respect Apple HIG & Material Design (`Inter` fallback typography in Material Android alongside iOS SF system fonts) so dynamic type, RTL mirroring, VoiceOver, and dark mode work automatically. Build on **`8pt` grids (`4pt` half-steps)**.
  - **Mobile color semantics**: Tune light/dark pairs carefully (raise dark-mode lightness and lower saturation to stop halo vibration); compile centralized palette tokens (`Mobile Design Toolkit`) into `.xcassets` and XML resources (`textPrimary`..`textTertiary`, `iconPrimary` vs `iconSecondary`, reserving blue `link` tint exclusively for app-bar chrome and primary view CTAs so blue never loses salience). Pair native fluidity (swipe triage) cleanly with Share-menu escape hatches back to responsive web URLs.

Sources: `primer/design` `content/native/{cli,desktop,mobile}/*.mdx`; `https://primer.style/accessibility/foundations/cli-accessibility/`.

## Agent application notes

- `[Universal]` Across any product copy, error UI, or CLI you generate: enforce sentence case without trailing periods on controls, active verb+noun buttons, zero humor/blame in failure states, full localized/clear timestamps without cryptic `1m`/`2mo` tokens, and `aria-hidden="true" focusable="false"` on decorative SVGs beside visible text labels.
- `[Primer]` Reach first for `@primer/octicons-react` (explicit `size={16|24}`, relying on inherited `currentColor`), `<RelativeTime>` wrapped safely when precision matters, and CLI conventions aligned with live `cli-accessibility/` guidelines.

## Gaps, caveats and source issues

- **Retired `/native/` documentation portal**: `https://primer.style/native/` now redirects to `/` cleanly, with only **CLI accessibility** actively expanded on the live site (`/accessibility/foundations/cli-accessibility/`). Archived `primer/design` `content/native/` docs (captured July 2025) remain useful context for `gh`/Desktop/Mobile design rationale, though exact Mobile/Desktop app component libraries evolve privately inside GitHub's native repos.
- **Staff-only brand content guide**: Both `content.md` (`design`) and `brand.github.com` defer deeper editorial/brand voice rules to private `github/brand/docs/how-to-write-at-github.md` and `content.md`, as well as staff-only Figma links (`Octicons`, `Mobile Design Toolkit`, `Primer Command-Line`) and internal Drive style folders.
- **Live `foundations/content/` delta**: The archived `primer/design` `content.mdx` file contains `<!-- TODO -->` comments under Dates and Numbers where the newer live Next.js documentation later filled in date-interval guidance and expanded UX writing examples alongside `RelativeTime`.
