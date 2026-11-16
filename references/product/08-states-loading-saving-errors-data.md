# System States: Loading, Saving, Empty States, Degradation, and Data Presentation

> Consolidates Primer's patterns for perceived performance and loading feedback, explicit vs. automatic persistence, honest empty and degraded states, and high-clarity charts and data tables.

## Contents

- [Key takeaways](#key-takeaways)
- [Loading states and latency calibration](#loading-states-and-latency-calibration)
- [Saving patterns and persistence trust](#saving-patterns-and-persistence-trust)
- [Empty states and Blankslate](#empty-states-and-blankslate)
- [Degraded experiences and partial outages](#degraded-experiences-and-partial-outages)
- [Data visualization and DataTable design](#data-visualization-and-datatable-design)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Universal]` **Calibrate loading UI to wait duration**: Suppress indicators under **`1s`** to avoid perceived sluggishness, use subtle indeterminate feedback (`Spinner` / Skeletons) for **`1–3s`**, use determinate `ProgressBar`s at **`3–10s`**, and background non-blocking workflows past **`10s`**.
- `[Universal]` **Never mix explicit and automatic saving in one form**: Keep declarative inputs (`TextInput`, checkboxes, radios, `<select>`, multi-select filters) explicitly submitted; reserve auto-saving for imperative switches (`ToggleSwitch`, `SegmentedControl`, standalone single-select menus).
- `[Universal]` **Never disguise outages as empty states**: Showing *"No repositories yet"* during a backend read failure tricks users into fearing data loss. Distinguish unavailable services honestly while preserving primary workflows wherever secondary chrome fails.
- `[Universal]` **Enforce visual clarity caps in charts and tables**: Limit line/stacked-area series to `<=5`, differentiate series with stroke styles and marker shapes (not hue alone), use `tabular-nums` right-aligned figures in flat `DataTable`s, and leave empty table cells blank rather than `"-"`.

## Loading states and latency calibration

### Wait-time decision matrix `[Universal]` / `[Primer]`

Because network latency, device speed, and server load fluctuate unpredictably, match loading fidelity cleanly to expected delay:

| Expected Delay | UI Strategy | Rationale & Component |
| --- | --- | --- |
| **`< 1 second`** | **Show no loader** (`delay` prop) | Flashing a spinner for brief responses makes an app feel slower and adds noise. Use `@primer/react` `<Spinner delay="short" />` (`300ms`) or `delay="long"` / `true` (`1000ms`) to suppress sub-second flicker. |
| **`1–3 seconds`** | **Indeterminate indicator** | Too short for users to read ETA or percentage bars cleanly. Use `Spinner` for small inline/button waits or **`Skeleton*`** shapes (`SkeletonText`, `SkeletonAvatar`, `SkeletonBox`) mimicking layout blocks for larger views. |
| **`3–10 seconds`** | **Determinate progress + text** | Shows measurable movement (`ProgressBar` + explicit progress label) or rarely an interstitial creation screen for heavy mutations (e.g., creating/forking a repository). |
| **`> 10 seconds`** | **Background task + determinate status** | Unblock the rest of the workspace (e.g., review PR diffs while CI checks run) and surface persistent notification updates rather than trapping the page. |

### Loading lifecycle, incremental streaming, and scoping `[Universal]`

- **Four lifecycle phases**: Pre-trigger -> In progress -> Resolved success (navigate or confirm subtly) -> Failed (clear error explanation + explicit **Retry** button or next step).
- **Incremental disclosure & priority order**: Render each loaded card/row immediately instead of blocking the entire feed on the slowest payload; prioritize above-the-fold identity (**Skeleton -> Text -> Actions -> Media**).
- **Consolidate adjacent loaders**: Replace dozens of tiny synchronous spinners on cards/fields with a single container loader to cut visual noise and avoid screen-reader chatter. Avoid layout shift between placeholder skeletons and final dimensions.
- **Loading accessibility (`loading.md`)**:
  - Give a cluster of skeletons **one shared live status announcement**, not one per skeleton element. Set `aria-busy="true"` on regions mid-update until final DOM swaps finish.
  - Pre-mount `role="status"` regions for dynamic filter counts (`"5 items match the filter"` / `"No items match"`).
  - Never put HTML `disabled` on a triggered Save button while loading (drops keyboard focus / Forms-mode discoverability); hold focus via `aria-disabled="true"` alongside live announcement, and suppress duplicate submissions cleanly.
  - Keep subtle spinner/progress movement active even under `prefers-reduced-motion` (reassures users the UI hasn't frozen), keeping motion mild with zero strobing or dramatic travel.

Sources: `https://primer.style/product/ui-patterns/loading/`, `.../components/{spinner,skeleton-text,progress-bar}/`.

## Saving patterns and persistence trust

### Explicit vs. automatic separation `[Universal]`

Mixing manual-save fields with instant auto-save widgets inside one form breaks mental models and risks unintended commits:

- **Declarative controls -> Explicit Save REQUIRED**: `TextInput`, `Textarea`, `CheckboxGroup`, `RadioGroup`, native `<select>`, and multi-select dropdowns (`SelectPanel` modal with `Save` & `Cancel`). Screen reader users exploring radios or native `<select>` options via arrow keys activate each option as focus moves—auto-saving mid-navigation would commit unintended preferences. Even multi-select dropdowns should commit via explicit `Save`/`Cancel` rather than saving silently on outside click when consequences matter.
- **Imperative controls -> Automatic Save**: `ToggleSwitch` (acts like a light switch), `SegmentedControl` (immediate view/mode picker), and standalone single-select menus not inside a multi-field form. **Rule**: Automatic updates must look visibly self-evident without fake *"Saved"* badges that mimic validation messages; surface explicit feedback only if async saving is slow or fails.

### Action naming, button state, and alignment `[Universal]` / `[Primer]`

- **Button labels**: Active verb + object (*"Save public profile"*, *"Add SSH key"*, *"Update description"* — never vague *"Done"* or *"OK"*). Use `primary` for whole-page/modal submits and `secondary` for partial-page section submits.
- **Never disable Save on invalid/unchanged forms**: Keep submit reachable (`Tab`-focusable) so users can inspect missing constraints on submit. Limit **one active Save / inline edit mode per section/page** to prevent ambiguous multiple Save buttons.
- **Spatial placement rules `[Primer]`**:
  - **Standard forms (default)**: **Bottom-left** (`Save` left, `Cancel` to its **right**), matching top-to-bottom left-aligned reading/eye flow.
  - **Dialogs & comment composers**: **Bottom-right** (`Cancel` to the **left** of `Save`/`Comment`), matching OS modal conventions and messaging workflows.
  - **Inline single-field edits**: Tightly adjacent right beneath the active field.
- **Forgiveness & navigation protection**: Guard unsaved form routes with browser `beforeunload` warnings; require `ConfirmationDialog` before destructive submits; provide **Undo / Revert** options after consequential updates (e.g., revert merged PR). When async creation redirects (repo fork/template setup), hold the user on a reassuring waiting screen preserving config visibility.

Sources: `https://primer.style/product/ui-patterns/saving/`.

## Empty states and Blankslate

Use empty states (`Blankslate`) when a surface lacks items cleanly, choosing tone strictly by cause:

| Empty Cause | Graphic | Primary & Secondary Copy | Actions |
| --- | --- | --- | --- |
| **First-time / No data created yet** | Octicon, product emblem, or welcoming brand illustration (FTUX only) | Warm, human title naming feature value + brief how-to subtitle (or embedded setup `code` block for dev tools like Packages). | Encouraged: **1 Primary button/link** starting creation + optional Secondary `"Learn more about X"` link underneath. |
| **Temporarily empty** (e.g., Inbox zero) | Clean neutral Octicon | Concise factual statement (*"No notifications yet"*). | Optional single relevant filter-clear or setup link. |
| **Error / Load failure** | **`alert-24` Octicon** (`fgColor-muted`; never playful/branded artwork) | Concise human summary of failure (*"Repositories could not be loaded"*) + recovery advice (`"Try again"`, contact support). Omit obscure internal server topology/stack errors; include HTTP codes only when actionable diagnostics. | Single recovery action (`Try again` / status link); **omit secondary `"Learn more"` links**. |

- **Container-adaptive sizing `[Primer]`**: `Blankslate` uses CSS container queries to shrink padding and font scales cleanly when embedded inside narrow panes; add `border` when multiple siblings share the viewport so bounds remain clear.

Sources: `https://primer.style/product/ui-patterns/empty-states/`, `.../components/blankslate/guidelines/`.

## Degraded experiences and partial outages

Primer treats graceful degradation as **containing blast impact without hiding reality**:

- **Primary vs. Secondary outage triage `[Universal]`**:
  - If **primary workflows** fail (e.g., issue body/title missing, form completely unreadable), replace the view cleanly with a full error page or `Blankslate`.
  - If **secondary chrome** fails (notification dots, badge counters, side widgets, avatar hovercards), **render the working workspace** and degrade only affected regions cleanly.
- **Messaging discipline during outages `[Primer]`**:
  - **Critical wide impact**: Render a page-top `warning` `Banner` above global nav linking to status/troubleshooting.
  - **In-page outage cap**: Keep inline error placeholders **to `<=5` per page** so errors don't overwhelm primary tasks.
  - **Never render broken interpolation** (`"undefined of undefined"`) or false empty states where existing user data is known to exist.
  - **Subtle styling**: Pair unavailable `Blankslate` icons with `fgColor-muted` (`alert-24`), and avoid flooding screens in saturated yellow/red `fgColor-warning` blocks.
- **Element-specific rules**:
  - **Counts & notification badges**: Hide missing metrics silently (optionally tooltip on interactive tabs explaining missing counts); never render `'0'` when counts fail to fetch!
  - **Navigation chrome**: **Never suppress the global header** (strands users). Hide individual broken dynamic nav links; keep lazy-loaded drawer errors explicit via internal `Banner` when spinners fail.
  - **Degraded forms & actions**: Preserve entered input cleanly; disable *only* unreadable fields with inline explanations below; **never hide critical workflow buttons** (`Request changes`, `Submit`, `Comment`)—keep non-functional core actions focusable as **`inactive`** buttons that explain the outage on activation, whereas non-critical buttons (e.g., reaction pickers) can be hidden quietly. Never put non-focusable error spans under hover-only tooltips.

Sources: `https://primer.style/product/ui-patterns/degraded-experiences/`.

## Data visualization and DataTable design

### Chart anatomy, limits, and non-color differentiation `[Universal]` / `[Primer]`

- **Supported Primer chart types**: Bar/Column, Line, Area, and `ProgressBar` (**Donut charts** and **Sparklines** are listed as currently unsupported for new custom Primer creation; if donut/pie views exist elsewhere, cap at **`<=5` slices** merging long tails into `"Other"`).
- **Mandatory anatomy**: Concise header, optional subtitle (mandatory if axis titles omitted), axis labels (`4.5:1` contrast), `3:1` axis borders (`--borderColor-default`; gridlines require no contrast floor), `2px` marks/plot lines, tooltips for inspected points, and a **toolbar menu** exposing CSV download and an accessible tabular data preview for simple charts.
- **Density limits**:
  - **Line charts**: **`<=5`** lines (split dense series into small multiples).
  - **Stacked area charts**: `<=3` lines ideal, **`<=5` max** (above 5, switch to unstacked line chart).
  - **Bar / Column**: **`<=10`** bars/columns when comparing distinct categories (long continuous time series may exceed carefully); **Grouped**: `<=4` series/group, `<=6` groups; **Stacked**: `<=5` segments/bar, `<=10` bars total.
- **Color & accessibility rules**:
  - Every chart mark must meet **`3:1` contrast vs. background** (`4.5:1` for text/axis labels). **Muted fills never stand alone**—always border muted area/bar fills with a `>=3:1` solid stroke (`data-vis` tokens).
  - Because no large palette preserves `3:1` contrast between *every* adjacent color across CVD types, encode series redundantly via **stroke dash patterns** (`solid` first, then distinct dashes), **distinct point markers** (`circle` first), varying lightness, or **`3:1` dividers/gaps** separating adjacent `ProgressBar`/chart segments.
  - Legends required whenever `>1` dataset appears (unless items are directly labeled inline).

### `DataTable` usage and layout rules `[Universal]` / `[Primer]`

- **When NOT to use `DataTable`**: Use lists/headings/trees when most cells would sit empty, content involves nested hierarchy (`NavList`/`TreeView`), prose spans paragraphs, or cells require rich spreadsheet inline editing (use a Data Grid instead). Tables are strictly for flat 2D item-attribute comparison.
- **Design & readability rules**:
  - **Default sort**: Intuitive ordering (newest first or alphabetical col 1); support clear per-column `aria-sort`. Keep headers minimal; omit visible header text on row-actions column.
  - **Row density & transpose check**: Keep column count compact—consider transposing rows/columns or choosing cards if columns will always outnumber rows. Start pagination around **`20` rows/page** (hide pagination when single-page; disable only what isn't needed).
  - **Atomic, scannable cells**: 1 data point per cell so sorting stays unambiguous; keep cell copy concise; **right-align numeric columns with `font-variant-numeric: tabular-nums`**; wrap long text cleanly before truncating (use truncation only with fixed column widths + accessible full view).
  - **True blanks over dummy punctuation**: **Leave genuinely empty cells blank** (or print explicit descriptive copy like `"None configured"`)—**never insert `"-"`, `"x"`, or emoji placeholders**, which read noisily/confusingly on screen readers.
  - **Avoid duplicate titles**: Omit the internal table header if the enclosing `PageHeader` already names the table identically (associate via ARIA cleanly).
  - **Narrow screen adaptation**: Configure per-column sizing (`Grow` [`minmax(auto, 1fr)` default], `Grow or collapse` with `minWidth` floor to prevent `0px` collapse, `Auto` [avoid on volatile string lengths], `Explicit`, plus fixed `Action column`), drop non-essential columns responsively, or allow keyboard-focusable horizontal scroll. Loading rows render aligned skeleton cells matching icon/alignment dimensions.

Sources: `https://primer.style/product/ui-patterns/data-visualization/`, `.../components/data-table/{guidelines,accessibility}/`, `.../components/progress-bar/accessibility/`.

## Agent application notes

- `[Universal]` Across any framework (Tailwind, MUI, HTML/CSS dashboards, TanStack Table, Recharts/ECharts), directly enforce latency-tuned loading thresholds, `300–1000ms` spinner debounce, separated explicit-vs-auto save controls, read-only outage honesty (`<=5` notices, no false empties), `<=5` line/series chart caps with pattern dashes + CSV/table fallbacks, and clean flat tables with right-aligned `tabular-nums` + genuinely blank empty cells.
- `[Primer]` Use `<Spinner delay="short">`, `<SkeletonText>`/`<SkeletonAvatar>`/`<SkeletonBox>`, `<ProgressBar progress={...}>`, `<Blankslate>`, and `<DataTable>` with `<PageHeader>` rather than ad-hoc HTML tables or raw loading text.

## Gaps, caveats and source issues

- **Experimental loading/empty packages**: In live `@primer/react` snapshot docs, `SkeletonText`/`SkeletonAvatar`/`SkeletonBox`, `DataTable`, and `Blankslate` carry varying React readiness flags (verify in [09-scenario-patterns-and-components.md](./09-scenario-patterns-and-components.md) before production adoption) and `degraded-experiences/` still mixes legacy `fg.warning` / `"blankstate"` typos alongside modern `--fgColor-muted` CSS variables.
- **No shipped chart library in `@primer/react`**: While Primer documents exhaustive chart anatomy, tokens (`data-vis`), and limits in `ui-patterns/data-visualization/`, `@primer/react` itself ships only `<ProgressBar>` rather than full Line/Bar/Area chart components (teams integrate `--data-*-color` tokens with third-party charting libraries).
