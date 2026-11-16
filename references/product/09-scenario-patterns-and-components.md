# Scenario Patterns and Product Component Catalog

> Consolidates Primer's 9 task-based Scenario Patterns (Copy, Create, Customize, Delegate, Delete, Edit, Filter, Search, View), the live Product component status & usage inventory, Internal Component boundaries, and React deprecation rules.

## Contents

- [Key takeaways](#key-takeaways)
- [Scenario Patterns: task-first UX specifications](#scenario-patterns-task-first-ux-specifications)
- [Product component catalog by function](#product-component-catalog-by-function)
- [Metadata badge and card composition rules](#metadata-badge-and-card-composition-rules)
- [Internal GitHub components boundary](#internal-github-components-boundary)
- [Deprecations and legacy React migration targets](#deprecations-and-legacy-react-migration-targets)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Primer]` **Scenario Patterns complement UI Patterns**: While *UI Patterns* describe structural solutions (forms, navigation, loading), Primer's **9 Scenario Patterns** define end-to-end behavioral rules anchored in user verbs—ensuring consistent surface weight, shortcut safety, focus movement, URL persistence, and reversible safeguards.
- `[Universal]` **Scale surface and friction strictly to scope and blast radius**: Keep 1–2-field edits or creations inline, use Dialogs/Side Sheets to preserve surrounding workspace context, promote high-consequence multi-section flows to dedicated URLs, and calibrate delete safeguards from instant Undo to `ConfirmationDialog` to type-to-confirm modals.
- `[Primer]` **Track live `@primer/react` maturity carefully**: Across the 62 documented public Product components, **52** are React `ready`, **7** are `experimental` (`Blankslate`, `Card`, `DataTable`, `InlineMessage`, `SkeletonAvatar`, `SkeletonText`, `UnderlinePanels`), **1** is `draft` (`KeybindingHint`), and **2** are `deprecated` (`CircleBadge`, `TextInputWithTokens`). Keep internal `github-ui` packages isolated from external apps and verify subpath exports before adopting experimental modules.

## Scenario Patterns: task-first UX specifications

| Scenario | Core Selection & Flow Rule | Focus, Announcements & Error Recovery |
| --- | --- | --- |
| **Copy** | Single-click always-visible `IconButton` (`invisible` variant, `CopyIcon`) directly beside value (**never** hover-only, **never** confirm modal); embed inside read-only `TextInput` as trailing action + auto-select input on focus. Offer protocol choice (HTTPS/SSH) only when target truly varies. | Swap to success `CheckIcon` + `"Copied!"` tooltip for **`~2s`** **only after clipboard Promise resolves** (never optimistically); inline button spinner if async fetch needed. Announce via hidden live region (same button label specificity: `"Copy branch name"`, never bare `"Copy"`). On rejection, show critical `AlertIcon` + live region + manual copy path. Hide unavailable copy triggers (no dead disabled button). |
| **Create** | **Lightest matching surface**: 1–2 fields / list append -> inline; handful of fields keeping parent context -> `Dialog`; consequential or refresh-worthy setup -> dedicated page. Infer defaults cleanly (skip form entirely for 1-click creation where safe); use `PlusIcon` + `"New [thing]"` / `"Create [thing]"`. Hide triggers lacking permissions. Distinguish mutually exclusive output modes at submit rather than duplicating buttons. | Keep submit `primary` button enabled; show button loading (`role="status"` progress); preserve entered values on error + use `sessionStorage` for long forms; warn on dirty dialog close. On submit failure, focus `critical` form-top `Banner` (page/form errors) or first invalid field. On Dialog close, return focus to trigger; inline create focuses newly inserted item. Omit visible success banner if new item renders/redirects immediately (still announce to AT). |
| **Customize** | Deliver accessible defaults first and honor OS preferences (theme, reduced motion, link underlines). Put view-scoped controls inline right beside what they alter; centralize app-wide preferences with deep links from context and account-level persistence (never overwrite shareable view URLs). Pick lightest control (`ToggleSwitch`/`SegmentedControl` = immediate auto-save; radios/checkboxes/forms = explicit single Save). Provide bulk reset only on multi-control forms. | Visual selected state confirms on-screen immediate tweaks; announce off-screen updates (e.g., list reorder) via live region. Never auto-save declarative radios/checkboxes (breaks screen-reader arrow browsing) and never confirm individual unsaved choices prematurely. Retain user choices + show critical `Banner` if save fails. |
| **Delegate** *(Human & AI Agent)* | Keep workflow rooted in origin context. Confirm receipt cleanly (`Button` loading -> persistent inline status indicator, not endless spinner). Visually separate people (`Avatar`) from agents (`CopilotIcon`; nest agent beneath assignee when both act). Maintain inspectable decision trail (`commits`/`comments` or summarized redacted agent logs). Ask explicit clarifying options when ambiguous; provide pause, steer/edit, resume, accept, request changes, and revert gates (**no unreviewed auto-merge**). | Announce incremental milestones & failures immediately in a live region paired with non-color icon/text badges. Report partial success honestly alongside next steps without dropping progress. Make long-running delegated jobs URL-addressable. |
| **Delete** | Calibrate friction to blast radius: routine unlink = Undo window (`"Remove"` wording, never `"Delete"` if item persists elsewhere); permanent single item = `ConfirmationDialog` + `ActionList.Item variant="danger"` + `TrashIcon`; irreversible repo/org destruction = danger-zone Settings + **type-to-confirm `Dialog`** (manual `returnFocusRef`). Offer reversible `"Disable"` alternatives where dependent systems exist. | CTA uses `"Delete [target]"` (**never** `"Yes"`/`"OK"`/`"Confirm"`, except intentional typing phrase `"I understand..."`). Page-level delete redirects to parent list; inline delete focuses **previous sibling**, or parent heading/container (never drop focus to `<body>`). Announce deletion via live region without noisy success banners; on error, keep dialog open with internal critical `Banner`. |
| **Edit** | Lightest room-giving surface (inline -> `Dialog` -> settings page); warn/preview cascading side effects (e.g., branch rename retargeting PRs) + support Undo. Allow **only 1 inline edit open at a time** cleanly per page. `PencilIcon` + target-specific accname; hide unpermitted edit triggers; keep triggers focus+hover accessible (**never hover-only**). Keep inline formatting previews non-destructive. Use **explicit Save** for authored content; keep single-line `Enter` submits vs multi-line `Enter` newlines. | Visible **Cancel** alongside Save **plus** `Esc` reverting text to pre-edit state (prompt dirty discard only if actually modified). Show loading state on Save; never clobber concurrent remote edits (detect and present conflict diff); keep sticky bars clear of focused inputs; protect substantial edits in local draft storage; move focus into inline field on edit open and back to trigger on close. |
| **Filter** | **Filter narrows already-rendered items**. Control ladder: `ActionMenu` (immediate small known options) -> `SelectPanel` (searchable item picker) -> structured qualifier bar (multi-facet queries, full available width above list). Never fire network fetches on every keystroke while typing query tokens. | Persist filter state in shareable **URL params**. Clearly distinguish `"No items match active filters"` (displaying active criteria + obvious Reset/Revert CTA) from `"Empty list"`. Pair visual active-filter badges with AT labels (`"Filter alerts"`), validate syntax inline, show inline `Banner`/`InlineMessage` on failed fetch (never rollback silently), and announce updated counts via persistent `role="status"`. |
| **Search** | **Search finds items not yet visible** (distinguish label `"Search [target]"` cleanly from `"Filter"`). `<form role="search">` + `SearchIcon` placed visible at top of content without forced detour page. Fast local hits update incrementally; heavy queries wait for explicit submit. Persist query in URL. | Show `TextInput` `loading` (`loaderText`) + skeleton results (`aria-busy="true"`). Show empty state naming the search query on 0 hits (never leave stale items or void blank screen); advertise keyboard triggers (`/`, command palette) via visible `KeybindingHint` rather than vanishing placeholder text; include trailing Clear `IconButton`; announce count changes politely. |
| **View** | Match display surface to interruption cost: primary body inline -> contextual preview/details in right `Side sheet` (narrow bottom/fullscreen fallback) -> transient read-only snippet/diff in centered `Dialog` (never lock deep exploration inside modal focus traps). Use parent-detail `SplitPageLayout` cleanly + previous/next item stepping inside queues/inboxes. Isolate slow/broken sub-panes inline instead of failing whole screen. | Keep focus on list selection when detail pane swaps in split views cleanly—announce change in live region instead of stealing focus. Encode active tab, selected row, and highlighted code lines in shareable URLs with copyable permalink controls; signal selection beyond color alone; give hovercards non-hover click/keyboard parity. |

Sources: `https://primer.style/product/scenario-patterns/{copy,create,customize,delegate,delete,edit,filter,search,view}/`.

## Product component catalog by function

Statuses reflect the live `primer.style/product/components/` docs snapshot (`-` = React-only in unified docs; note Rails `primer_view_components` is in global maintenance mode, though documented components show Rails `ready`).

### Actions & Buttons `[Primer]`

| Component | React / Rails | Purpose & Key Guardrails |
| --- | --- | --- |
| **`Button`** | `ready` / `ready` | `primary` (sparingly: max `1` per group/page), `default` (previously called secondary styling visually), `invisible`, `danger`. Sizes `small` (`<24px` trap on 1-char label!), `medium` (default), `large`, `block`. Sentence case, single-line (no wrap), active verbs; primary CTA sits at the **end (right)** of `ButtonGroup`. Avoid `disabled`; use `inactive` + explanation if non-functional yet essential; use `loading` + live announcement. Multi-select menu trigger summary uses **`"Labels: 2 selected"`**, not comma-truncated strings. |
| **`IconButton`** | `ready` / `ready` | Icon-only button. Always requires `aria-label` (or visual label Tooltip mode); supports `keyshortcuts` / tooltip hint (`description`). |
| **`ActionBar`** | `ready` / `ready` | Horizontal `IconButton` strip (`small`/`medium`/`large`) with vertical dividers (`never at start/end`) and auto overflow menu; expands touch hit areas cleanly. Override `toolbar` role inside list rows. |
| **`ButtonGroup`** | `ready` / `ready` | Adjoining grouped buttons; max 1 `primary` button positioned at end. |
| **`Link`** | `ready` / `ready` | Hyperlink navigation (`href`); underlined inline or clear visual distinction. Never simulate buttons cleanly via anchors. |

### Navigation & Layout `[Primer]`

| Component | React / Rails | Purpose & Key Guardrails |
| --- | --- | --- |
| **`PageLayout`** / **`SplitPageLayout`** | `ready` / `ready` (`Split` React-only) | Page regions (`Header`, `Content`, `Pane`, `Footer`); responsive narrowing rules and pane sticky/resizable sizing (see [02-layout-spacing-responsive.md](./02-layout-spacing-responsive.md)). |
| **`Stack`** | `ready` / `ready` | Flexbox 1D responsive layout primitive (`direction`, `gap`, `align`, `justify`, `padding`); replaces `Box` layout spacing props. |
| **`PageHeader`** | `ready` / `-` | Main view top header with narrow-only `ContextArea` (`<- Parent` / compact breadcrumbs), `Default`/`Large`/`Subtitle` title variants, leading/trailing visuals & actions. |
| **`NavList`** | `ready` / `ready` | Vertical sidebar URL links (`NavList.Heading`, `NavList.Group`, `NavList.SubNav`); consistent leading visuals; dividers between groups only. |
| **`UnderlineNav`** | `ready` / `ready` | Horizontal URL-routed top tabs with automatic overflow `ActionMenu` and `aria-current="page"`. |
| **`UnderlinePanels`** | `experimental` / `ready` | Local non-routed tabpanels (`tablist`/`tab`/`tabpanel`); avoid `loadingCounters` until AT text alt ships. |
| **`Breadcrumbs`** | `ready` / `ready` | Deep hierarchy trail (up to 10 items default); built-in separator spacing. |
| **`Pagination`** | `ready` / `-` | Paginated URL bar placed directly beneath long lists; omit for single-page results. |
| **`TreeView`** | `ready` / `-` | File-explorer / parent-child tree widget (`Arrow` keys + `300ms` typeahead). |

### Forms & Selection Pickers `[Primer]`

| Component | React / Rails | Purpose & Key Guardrails |
| --- | --- | --- |
| **`FormControl`** | `ready` / `ready` | Programmatic `<label for>`, `.Caption`, and `.Validation` association (`as="legend"|"span"` support for fieldsets/SegmentedControls). |
| **`TextInput`** / **`Textarea`** | `ready` / `ready` | Single & multi-line text inputs (`leadingVisual`, `trailingAction` like Clear/Copy, inline loading indicator). |
| **`Autocomplete`** | `ready` / `ready` | Filterable suggestions overlay attached to text input. |
| **`Select`** | `ready` / `ready` | Browser `<select>` styled consistently for longer single-choice lists. |
| **`Checkbox`** / **`CheckboxGroup`** | `ready` / `ready` | Multi-select choices; place validation & `required` semantics at group level only. |
| **`Radio`** / **`RadioGroup`** | `ready` / `ready` | Mutually exclusive choices (<=5–7 options) inside explicitly saved forms. |
| **`SegmentedControl`** | `ready` / `ready` | Immediate-effect 2–5 option (<=6 icon-only) inline picker; `<ul>/<li>/<button>` semantics (**no `<a>` link mode** in React). |
| **`ToggleSwitch`** | `ready` / `ready` | Immediate binary switch (`<button aria-pressed>`); add live-region feedback when `loading` runs async. |
| **`ActionList`** | `ready` / `ready` | Single-column command/option list foundation (`leadingVisual` all-or-none, trailing shortcut/chevron/badge, `variant="danger"` for delete); `inactive` items omit nested tooltips cleanly. |
| **`ActionMenu`** | `ready` / `ready` | Overlay `ActionList` menu (`<=~15` choices); never embeds `<input>` filters (use `SelectPanel`). |
| **`SelectPanel`** | `ready` / `ready` | Filterable single/multi picker dialog (modal Save/Cancel vs nonmodal auto-apply; selected options pinned at initial open/reset). |
| **`TextInputWithTokens`** | **`deprecated`** / `-` | Legacy token tag input (`Button`/`Autocomplete`/`SelectPanel` patterns replace older monolithic input patterns). |

### Overlays & Disclosures `[Primer]`

| Component | React / Rails | Purpose & Key Guardrails |
| --- | --- | --- |
| **`Dialog`** | `ready` / `ready` | Modal (`aria-modal`, focus trap, required title + close `'X'`). Supports centered modal, side sheets, narrow bottom sheet, mandatory mobile fullscreen for forms; avoid backdrop click close on dirty forms; max 2 stacked modals. |
| **`ConfirmationDialog`** | `ready` / `-` | `role="alertdialog"` (`useConfirm()` hook supported); presets `296px`/`320px`/`480px`; focuses **Cancel** automatically on `danger`. |
| **`Overlay`** / **`AnchoredOverlay`** | `ready` / `ready` (`Anchored` React-only) | Building-block floating surface primitives (`getAnchoredPosition`, outside-click & `Esc` handling). |
| **`Popover`** | `ready` / `ready` | Caret-anchored lightweight callout (`onEscape`, `onClickOutside`) for subtle onboarding only; never critical errors. |
| **`Tooltip`** | `ready` / `ready` | Hover/focus string helper **primarily for `IconButton` visible labels**; never static elements or `disabled` controls. |
| **`Details`** | `ready` / `ready` | Enhanced native `<details>/<summary>` disclosure (`useDetails()`); pair Chevron/Fold icons carefully by semantics. |

### Feedback, Status & Loading `[Primer]`

| Component | React / Rails | Purpose & Key Guardrails |
| --- | --- | --- |
| **`Banner`** | `ready` / `ready` | Prominent page/section/dialog `<section>` alert (`info`, `warning`, `critical`, `success`, `upsell`). Max `1` banner/page, max `2` actions; never flush at top of open pages. |
| **`InlineMessage`** | **`experimental`** / `-` | Localized action/validation note (`critical`, `warning`, `success`, `unavailable`, `small`/`medium`). Verify TS prop union for `'unavailable'`. |
| **`Spinner`** | `ready` / `ready` | Indeterminate wait indicator (`small`/`medium`/`large`); supports `delay="short"` (`300ms`) / `"long"` (`1000ms`) and `srText` (`null` if external text already explains loading). |
| **`SkeletonBox`** | `ready` / `-` | Structural rectangular/custom placeholder box (`1–3s` waits, single group announcement). |
| **`SkeletonText`** / **`SkeletonAvatar`** | **`experimental`** / `-` | Typography-matched line skeletons & circular/square avatar shimmers. |
| **`ProgressBar`** | `ready` / `ready` | Determinate percentage or segmented horizontal bar (`>=3s` waits, `3:1` segment borders/gaps, adjacent text explanation). |
| **`Blankslate`** | **`experimental`** / `ready` | Container-query-responsive empty/FTUX/error placeholder (1 primary CTA + optional secondary link; alert icon + muted hue on outages). |

### Data Display, Typography & Metadata `[Primer]`

| Component | React / Rails | Purpose & Key Guardrails |
| --- | --- | --- |
| **`DataTable`** | **`experimental`** / `-` | Flat 2D structured table (`Column` sizing modes, sortable headers, skeleton cells, pagination buttons, blank empty cells). |
| **`Card`** | **`experimental`** / `-` | Grouped container (`as="section"` + `Card.Heading` for standalone regions; plain `<div>` inside `<ul><li>` lists **without headings**). |
| **`Timeline`** | `ready` / `ready` | Vertical activity log (`Timeline.Item`, `Timeline.Badge`, `Timeline.Body`, `Timeline.Break`); opt in to `primer_react_timeline_list_semantics` (`FeatureFlags`) for semantic `<ol>/<li>`. |
| **`Avatar`** / **`AvatarStack`** | `ready` / `ready` | User (round) vs Organization/App (square) avatars; inline overlapping stacks. |
| **`CircleBadge`** | **`deprecated`** / `-` | Replaced by modern `Avatar` / card / icon framing patterns. |
| **`Heading`** / **`Text`** / **`Truncate`** | `ready` / `ready` | Semantic/typographic wrappers (`Truncate` wraps CSS ellipsis safely on static text, never interactive controls). |
| **`RelativeTime`** | `ready` / `ready` | Accessible local/relative `<relative-time>` timestamp formatting (see [10-iconography-and-content-ux.md](./10-iconography-and-content-ux.md)). |
| **`StateLabel`** | `ready` / `ready` | Specific Issue/PR lifecycle pills (`issueDraft`, `issueOpened`, `issueClosed`, `issueClosedNotPlanned`, `draft`, `pullOpened`, `pullMerged`, `pullClosed`, `unavailable`). |
| **`Label`** / **`LabelGroup`** | `ready` / `ready` (`Group` React-only) | Static contextual metadata pills (`Default`, `Primary`, `Secondary`, `Accent`, `Success`, `Attention`, `Severe`, `Danger`, `Done`, `Sponsors`; `small`/`large`). |
| **`CounterLabel`** | `ready` / `ready` | Numeric pill beside nav tabs, headers, or buttons (always pair visually/programmatically with noun). |
| **`BranchName`** | `ready` / `-` | Monospace branch pill (`<a>` by default). |
| **`Token`** | `ready` / `-` | Compact removable/interactive object tag (Backspace/Delete support, leading avatar, truncated overflow `"+3"` label). |
| **`KeybindingHint`** | **`draft`** / `-` | OS-aware keyboard glyph renderer (`keys="Mod+K"`, `format="condensed"|"full"`, `size="small"|"normal"`). |

Sources: Verified directly across all 62 files under `https://primer.style/product/components/*/`; `.../react/theme-reference/`.

## Metadata badge and card composition rules

- **Choosing the right pill/badge `[Primer]`**:
  - **Issue/PR workflow state** -> `StateLabel` (includes icon + semantic hue).
  - **Static status/category tag** -> `Label` (`LabelGroup` manages wrap margins).
  - **Interactive/removable tag inside filters or lists** -> `Token` (supports focus, `onClick`, Backspace/Delete removal).
  - **Numeric item tally** -> `CounterLabel` inside tabs, buttons, or headings.
  - **Git ref / branch identifier** -> `BranchName`.
  - **Keyboard shortcut display** -> `KeybindingHint` (`draft`) or `<kbd>` HTML.
- **Choosing `Card` semantics (`card/accessibility/`) `[Universal]` / `[Primer]`**: Render standalone thematic regions as `<Card as="section">` with `Card.Heading` (`aria-labelledby` auto-wired) or `aria-label`. Inside lists/grids (`<ul>/<li>`), render `<Card>` as plain `<div>` and **omit headings inside individual list cards** so screen-reader heading/landmark rotors are not polluted; label repeated `"More options"` / edit controls with the card's target name.

## Internal GitHub components boundary

Primer's site documents **25 Internal Components** (`/product/internal-components/`) stored inside private staff packages (`github/github-ui`, `ui.githubapp.com`):

- `ActionListItems`, `BannerStack`, `ChartCard`, `CommandPalette`, `ControlGroup`, `CopilotAnimation`, `CopyToClipboard`, `DatePicker`, `DragAndDrop`, `EmojiAutocomplete`, `Filter`, `FilterCore`, `GitHubAvatar`, `InlineAutocomplete`, `LifecycleLabels` (note: exposes `AlphaLabel`/`BetaLabel`/`DeprecatedLabel`/`NewLabel` internally while public Product guidance deprecates Alpha/Beta phrasing in new UI copy!), `ListFilterButton`, `ListItemActionMenu`, `ListView`, `MarkdownEditor`, `MarkdownEditorViewSwitch`, `NestedListView`, `QuickSearch`, `SimpleListView`, `SimpleSelect`, `ui-commands`.
- **Agent constraint**: External apps **cannot install or import these packages**. Treat their specs (such as `@github/filter` qualifier behavior or `CopyToClipboard` 2s confirm state) strictly as **behavioral design references**, implemented in external apps using public `@primer/react` primitives or local equivalents.

Sources: `https://primer.style/product/internal-components/`.

## Deprecations and legacy React migration targets

| Deprecated / Legacy API | Current Replacement `[Primer]` |
| --- | --- |
| `<Box>`, `sx` prop, `themeGet()` / `@styled-components` | Plain elements or CSS Modules (`styles.module.css`) + CSS custom properties (`--bgColor-*`, `--stack-gap-*`) + `@primer/react` `Stack`, `clsx` (see [01-principles-and-governance.md](./01-principles-and-governance.md) & [04-color-system-and-tokens.md](./04-color-system-and-tokens.md)). |
| `theme` object (`theme-reference/`, legacy `fonts`, `space`, `fontSizes`, `radii`, `colors`) | Semantic Primer Primitives CSS variables (`--text-*`, `--base-size-*`, `--borderRadius-*`, `--fgColor-*`). |
| `<Flash>` (`react/migration-guides/primer-flash/`) | `<Banner>`. |
| `ButtonPrimary` / `ButtonOutline` / `ButtonDanger` / `ButtonInvisible` | `<Button variant="primary \| default \| danger \| invisible">`. |
| `<CircleBadge>`, `<TextInputWithTokens>` | `<Avatar>` / `Card` visuals; `SelectPanel` / `Autocomplete` + `<Token>`. |
| Legacy Rail CSS-only / `Primer::BlankslateComponent` (`primer/css` utilities & `.tooltipped`) | `@primer/react` equivalents (`Button`, `Dialog`, `SelectPanel`, `Tooltip`, `Banner`); Rails `primer_view_components` remains in maintenance mode (bug/security fixes only). |
| `React` -> `SelectPanel` v1 / `Overlay` legacy flags | See `product/getting-started/react/migration-guides/primer-experimental-selectpanel/` and always enable `FeatureFlags` like `primer_react_timeline_list_semantics` on new Timeline builds. |

Sources: `https://primer.style/product/getting-started/{react,rails}/`, `.../react/theme-reference/`, `.../react/extending-components/`.

## Agent application notes

- `[Universal]` Use the **9 Scenario Patterns** as a behavioral design spec before writing code in any framework: decide inline vs dialog vs page surface, pick Undo vs ConfirmationDialog vs type-to-confirm, wire URL state cleanly for View/Filter/Search/Delegate, and include keyboard/live-region paths for every mutation.
- `[Primer]` Check component stability before introducing experimental components (`Card`, `DataTable`, `InlineMessage`, `SkeletonText`, `UnderlinePanels`, `Blankslate`) or draft (`KeybindingHint`) in external `@primer/react` apps—verify exact subpath exports (`@primer/react/experimental` vs root `@primer/react`) in the project's installed `package.json`.

## Gaps, caveats and source issues

- **Internal doc links in Scenarios & Internal Components**: Each Scenario Pattern links to staff-only `github/scenarios` issue trackers (`label:scenario`), and all 25 Internal Components resolve Storybook/Source URLs to private hosts (`ui.githubapp.com`, `github/github-ui`).
- **`LifecycleLabels` vs. `feature-onboarding/` copy conflict**: Internal package `LifecycleLabels` documents `<AlphaLabel>` and `<BetaLabel>` components internally, whereas `product/ui-patterns/feature-onboarding/` explicitly states GitHub retired Alpha/Beta terminology in user-facing UI in favor of **`Private preview`** and **`Public preview`**.
- **Timeline semantics default mismatch**: `@primer/react` `Timeline` keeps `primer_react_timeline_list_semantics` disabled by default (`false`) despite being GA in GitHub production—agents building new Timelines must opt in explicitly with `<FeatureFlags>`.
- **Minor typo on `AnchoredOverlay` accessibility URL**: Live route uses misspelled slug `/product/components/anchored-overlay/acessibility/`.
