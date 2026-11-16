# Navigation, Information Architecture, and Overlay Structure

> Synthesizes Primer's navigation principles, transition models, tabbed/sidebar component selection trees, responsive wide-to-narrow transformations, and dialog/sheet spatial rules.

## Contents

- [Key takeaways](#key-takeaways)
- [Navigation principles and transition types](#navigation-principles-and-transition-types)
- [Choosing navigation components](#choosing-navigation-components)
- [Responsive navigation and narrow-viewport transformations](#responsive-navigation-and-narrow-viewport-transformations)
- [Dialogs, side sheets, and overlay boundaries](#dialogs-side-sheets-and-overlay-boundaries)
- [IA and accessibility invariants](#ia-and-accessibility-invariants)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Universal]` **Never obscure location or overload navigation**: Always make current context explicit (`aria-current`, active state, `PageHeader`, or `Breadcrumbs`), trim secondary chrome, and keep navigational controls directly adjacent to the content region they govern.
- `[Universal]` **Separate URL navigation from in-page state changes**: Route/URL changes belong in links (`NavList`, `UnderlineNav`, `Breadcrumbs`, `Pagination`); in-page view switches belong in `UnderlinePanels`, `SegmentedControl`, or `ActionList`/`Dialog`. Never mix URL-changing links and local panel switches inside the same tab bar.
- `[Universal]` **Cap tab hierarchy cleanly**: Never exceed two levels of tab hierarchy and never stack horizontal tab bars directly on top of one another—promote upper levels into a vertical sidebar (`NavList`).
- `[Universal]` **Adapt sidebars by role on narrow screens**: Link-index sidebars collapse back to parent index routes paired with `<- Back` links or `Breadcrumbs` on child views; filter sidebars move into header-triggered `ActionMenu`s (`<=15` options) or bottom sheets (`>15` options).

## Navigation principles and transition types

Primer frames navigation around three core goals: clear orientation (users always know where they are), streamlined choices (surface primary destinations without overwhelming clutter), and proportional transitions (reserve full context shifts for genuine task switches).

### Contextual shift taxonomy `[Universal]`

| Transition Type | URL Behavior | Expected Behavior & Recovery |
| --- | --- | --- |
| **Entirely new page** | Updates URL (soft SPA transition or full navigation) | Use parent-to-child links paired with a `Breadcrumbs` trail or Back link; guard against losing unsaved changes before navigating away. |
| **Same page, different state** | Optional (avoid full page reload) | Filters, sorts, or client-side panel switches update view state inline near the affected region. |
| **New spot on same page** | Updates URL if navigating to an `#anchor` | Scrolls/moves focus to target heading or form input without changing page content. |
| **Moving into a Dialog** | Usually unchanged | Lightweight, single-context task overlay that dims/inerts background chrome; always restores focus cleanly on close. |

Sources: `https://primer.style/product/ui-patterns/navigation/`.

## Choosing navigation components

### Primary selection matrix `[Primer]` / `[Universal]`

| Pattern / Component | Semantic Role & URL Rule | Best Used For | Key Constraints |
| --- | --- | --- | --- |
| **`NavList`** | `<nav>` + `<ul>/<li>/<a href>`; items are links changing URL (plus collapsible parent subnav `<button>`). | Settings sidebars, parent-detail vertical navigation. | **Links only** (use `ActionList` for mixed actions); name region via `NavList.Heading`; apply leading icons consistently (all items or none); dividers only separate **groups**, never individual items. |
| **`TreeView`** | `tree` widget; URL change optional. | Hierarchical file trees, deep code/symbol browsers. | Emulates OS file explorer, not generic marketing/site navigation; arrow-key roving focus rather than Tab-per-item. |
| **`UnderlineNav`** | `<nav>` + `<ul>/<li>/<a href>` + overflow `<button>`. | Switching sibling subpages with unique URLs under one parent scope. | Preselect default current tab (`aria-current="page"`); limit count (`<ActionMenu>` overflow handles overflow); align left matching content width; don't rely on icons/tooltips instead of readable view titles. |
| **`UnderlinePanels`** | `role="tablist"` / `tab` / `tabpanel`; URL unchanged. | Switching 2+ related panels within the same page/state. | Each panel should be self-contained enough to complete its subtask without flip-flopping; never stack two horizontal tab bars directly atop each other (max 2 hierarchy levels total). |
| **`SegmentedControl`** | `<ul>/<li>/<button>` (or link role if URL-routed in custom code). | Immediate sort (`Newest`/`Oldest`), view format (`List`/`Grid`, chart type), or quick filter including an `"All"` option. | **2–5** text segments (or up to **6** obvious icon-only segments); short noun labels only; no multi-line wrapping, no overflow button, no dropdown nested inside a segment. *Primer caveat*: current `@primer/react` `SegmentedControl` does not natively render `<a href>`, so prefer `UnderlineNav` when URL changes. |
| **`Breadcrumbs`** | `<nav aria-label="Breadcrumb">` ordered links + current item. | Deeply nested pages without persistent section sidebars or `>=2`-deep child routes. | Default max **10** chain items; parent segments link cleanly; terminal item reflects current page context; built-in divider padding eliminates manual spacing gaps. |
| **`Pagination`** | Horizontal sequence of page `<a href>` links (or `<button>` inside `DataTable` fetch views). | Long server-paginated collections directly **below** the list/table. | **Every page must have a URL** in standard collections; **omit Pagination completely** if items fit on a single page; tune page size to avoid tedium or overload. |
| **`PageHeader`** | Main-region top header slot (`ContextBar` + `TitleBar` + description + local nav). | Establishing view identity, local hierarchy back-links, and header actions. | `ContextBar` shows by default on **narrow viewports only** (parent link / compact breadcrumbs + narrow actions); `TitleBar` uses `Default` (standard UI), `Large` (UGC like Issues/PRs/Discussions), or `Subtitle` (when title exists outside main pane in Split layout). Collapse trailing actions into overflow menu on narrow screens. |
| **`ActionMenu`** | `role="menu"` overlay triggered by button. | Compact command lists, quick single/multi-choice pickers, overflowing horizontal nav tabs. | Heuristic cap of **~15 items** before moving to `SelectPanel` or bottom-sheet `Dialog`; never put `<input>` filters or buttons inside menu items. |

### Tabbed interface decision tree `[Primer]`

When choosing how to switch views in one context:
1. **Switching content format** (e.g., *List* vs. *Grid*, chart style) within `<=5` options? -> If view visibility swaps inline like panels, use `SegmentedControl` configured as tabs/panels (or `UnderlinePanels`); if selecting a URL route, use `SegmentedControl` with `<a>` links (or `UnderlineNav`).
2. **Sorting a collection** (mutually exclusive sort keys)? -> Use `SegmentedControl` (button segments for client state, `<a>` segments/menu if URL-backed).
3. **Filtering a collection** with mutually exclusive presets including an `"All"` clear option -> `UnderlinePanels` (or `UnderlineNav` if URL-backed, ensuring `"All"` clears filter cleanly).
4. **Switching subpages across distinct URLs** -> `UnderlineNav`.
5. **Switching local panels without URL change** -> `UnderlinePanels`.

Sources: `https://primer.style/product/ui-patterns/navigation/`, `.../components/{nav-list,underline-nav,underline-panels,segmented-control,breadcrumbs,pagination,page-header,action-menu}/guidelines/` (and `nav-list/guidlines/`).

## Responsive navigation and narrow-viewport transformations

Desktop sidebars remain visible beside main content, while narrow viewports (`<768px`) transform by **what the sidebar controls**:

| Sidebar Role | Wide Viewport (`regular`+) | Narrow Viewport (`narrow`) |
| --- | --- | --- |
| **Index of detail links** (e.g., Settings sidebar `/{parent}/{detail}`) | Sidebar `NavList` stays pinned next to detail pane. | **Two-step mobile master-detail**: visiting `/{parent}` displays the full index list; visiting `/{parent}/{detail}` hides the sidebar and exposes a **`<- Back` arrow** in `PageHeader`'s context bar (1-level deep) or **`Breadcrumbs`** (`>=2` levels deep). |
| **Set of filter controls** (e.g., Marketplace / Search facets) | Sidebar filter panel next to results list. | Sidebar collapses cleanly; surface a **Filters** button in the main header (opening an **`ActionMenu`** for `<=~15` simple items, or a **Bottom Sheet `Dialog`** for `>15` or 2-level nested filter lists). Optionally turn the content heading itself into the filter dropdown trigger if active filter names describe the view—style it clearly with interactive hover/focus/pressed states and a trailing caret. |
| **Mixed links + filters** | Combined sidebar (`NavList` + facet groups). | Split behaviors: detail links live on the parent index route while active filter facets collapse into a narrow header button / bottom sheet. |

Sources: `https://primer.style/product/ui-patterns/navigation/#responsive-sidebar-navigation-patterns`.

## Dialogs, side sheets, and overlay boundaries

- **Structural anatomy (`Dialog`) `[Primer]`**:
  - **Header**: Required visible title (or visually hidden `.sr-only` only for specialized layouts like Command Palettes), optional subtitle, required visible close `'X'` button, optional secondary `IconButton`s.
  - **Subheader (optional)**: Fixed interactive slot for search inputs, filter bars, or compact local nav.
  - **Body**: Vertically scrollable (`16px` perimeter safe zone; `1px` top/bottom scroll border separators when scrollable); favor single-column layouts—**never put a sidebar `NavList` inside a Dialog** (promote multi-section editors to full pages).
  - **Footer**: Confirmation/submit action **right-aligned** (grouped with secondary Cancel), optional auxiliary links **left-aligned**; add divider rule above footer when body scrolls.
  - **Backdrop behavior**: Dims background and closes on outside click **unless** the dialog holds a form with potential unsaved input—suppress backdrop click dismissal on dirty/form dialogs to protect user work.
- **Placement variants `[Primer]`**:
  - **Centered modal (desktop default)**: `16px` minimum viewport margin on all edges. Adapts on narrow screens to either a **Fullscreen Dialog** (**mandatory whenever the dialog contains 1+ form inputs** on mobile) or a **Bottom Sheet** (secondary non-form tasks tied to current view; spans 100% width in portrait and caps at `480px` horizontally centered in landscape).
  - **Left Side Sheet**: Reserved **strictly for global navigation drawers**. Full viewport height, backdrop covering the page including top header.
  - **Right Side Sheet**: Reserved for global actions or quick record previews on **full-width pages** (avoid right side sheets on narrow centered containers where users must drag pointers far across whitespace; never allow simultaneous multi-sheet stacks or side sheets without modal backdrops).
- **Hard boundaries `[Universal]` / `[Primer]`**:
  - **Nesting limit**: Up to **2** nested dialogs max when unavoidable (`Esc` and backdrop click close top-most only, returning focus to trigger inside first dialog); `>=3` nested dialogs indicates flawed IA.
  - **Deep linking**: If users must share URLs directly into a view, build a dedicated route/page rather than auto-opening a dimmed modal overlay from query parameters.

Sources: `https://primer.style/product/components/dialog/guidelines/`.

## IA and accessibility invariants

- **Skip links `[Universal]`**: Provide skip links before dense repetitive chrome (global top bar) and before dense multi-cell widgets (`DataTable`) so keyboard and screen-reader users jump directly into `<main>` or past large tables.
- **Heading & tab parity `[Universal]`**: Preserve sequential DOM heading outlines (`NavList.Heading` automatically anchors `h2 -> h3` group hierarchy on settings pages) and ensure DOM source order matches left-to-right / top-to-bottom visual focus order.
- **Consistent location placement `[Universal]`**: Keep `Pagination` directly beneath its collection, `UnderlineNav`/`UnderlinePanels` directly atop switched panes matching their width, and contextual `Help`/parent links in uniform positions across sibling routes.

Sources: `https://primer.style/product/ui-patterns/navigation/#accessibility`, `.../tools-and-resources/checklists/engineering-checklist/`.

## Agent application notes

- `[Universal]` Apply Primer's **URL-change vs. local-state split**, **narrow master-detail (`<- Back` / `Breadcrumbs`) vs. filter drawer adaptation**, **fullscreen mobile modal rule for forms**, and **no sidebar-inside-dialog rule** across any web stack (React/Next.js, Tailwind, Vue, Rails) without needing Primer React packages.
- `[Primer]` Keep component imports idiomatic: `NavList` for route links, `UnderlineNav` for routed top tabs, `UnderlinePanels` for unrouted local tabpanels, `PageHeader` for responsive narrow context bars + titles, and `Dialog`/`SelectPanel` for transient subflows.

## Gaps, caveats and source issues

- **Live URL spelling quirk**: Primer's live documentation publishes NavList guidelines under the misspelled route `/product/components/nav-list/guidlines/` (linked verbatim by `nav-list/accessibility/`).
- **`SegmentedControl` link limitation**: Live `segmented-control/guidelines/` explicitly flags that current `@primer/react` `SegmentedControl` does not support `<a>` segments natively—do not use it for URL navigation until upstream link support lands.
- **Staff-only Figma links**: Visual anatomy diagrams across product guidelines link to private GitHub Figma files (`Interface-guidelines`), while inline screenshots/alt descriptions capture the structural rules publicly.
