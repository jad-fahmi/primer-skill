# Primer Accessibility Program, Foundations, and Engineering Contracts

> Consolidates Primer's WCAG 2.2 AA standards, semantic HTML/ARIA rules, focus and keyboard contracts, component behavior matrix, AI/CLI accessibility frameworks, and testing practices.

## Contents

- [Key takeaways](#key-takeaways)
- [Program standards and disability model](#program-standards-and-disability-model)
- [Semantic HTML, ARIA, links vs. buttons, and naming](#semantic-html-aria-links-vs-buttons-and-naming)
- [Focus management, keyboard navigation, and shortcuts](#focus-management-keyboard-navigation-and-shortcuts)
- [App-like experiences, reflow, motion, and live regions](#app-like-experiences-reflow-motion-and-live-regions)
- [High-risk patterns: toasts, tooltips, placeholders, truncation, and drag-and-drop](#high-risk-patterns-toasts-tooltips-placeholders-truncation-and-drag-and-drop)
- [Component ARIA and keyboard quick reference](#component-aria-and-keyboard-quick-reference)
- [Generative AI and CLI accessibility extensions](#generative-ai-and-cli-accessibility-extensions)
- [Design annotations, native mobile, and verification workflows](#design-annotations-native-mobile-and-verification-workflows)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Primer]` GitHub targets **WCAG 2.2 Level A and AA** conformance (all of WCAG 2.1 AA minus removed SC 4.1.1 Parsing, plus WCAG 2.2 additions) and shifts accessibility left into initial design and primitive components rather than retrofitting after audit failures.
- `[Universal]` **Native HTML first**: Follow the First Rule of ARIA—native `<button>`, `<a>`, `<nav>`, `<h1>–<h6>`, and `<label for>` elements provide keyboard activation, Forced Colors mapping, and Reader Mode parsing that ARIA-on-`<div>` cannot fully replicate.
- `[Universal]` **Never rely on hidden or ephemeral UI for essential info**: Avoid `title` attributes, placeholder-only labels, disabled submit buttons, auto-dismissing toasts, and tooltips on non-interactive elements (`div`, `span`, `p`).
- `[Universal]` **Automated tools catch only ~20–25% of accessibility barriers**: Agents must explicitly verify keyboard reachability, focus restoration on DOM mutation, 200%/400% zoom and `320×256` CSS px reflow, reduced motion, and accessible naming (`SC 2.5.3 Label in Name`).
- `[Primer]` **AI and CLI surfaces carry specialized contracts**: Generative interfaces require explicit authorship/state semantics, safe live-region polling, and reversible history; CLIs must decouple human prose from machine output, respect `NO_COLOR`, and retain persistent text summaries.

## Program standards and disability model

GitHub treats accessibility as an ongoing engineering fundamental rather than a pre-launch gate, embedding accessibility designers and training Engineering/Design Accessibility Champions.

- **Conformance target `[Primer]`**: **WCAG 2.2 Level A and AA** (with SC 4.1.1 Parsing omitted per WCAG 2.2). Note that U.S. Section 508 references WCAG 2.0 AA.
- **Four disability dimensions × three durations `[Universal]`**: Evaluate every flow across **Visual** (blindness, low vision, color vision deficiency), **Cognitive** (concentration, memory, reading, problem-solving), **Mobility** (dexterity, tremor, switch/voice/keyboard-only), and **Hearing** (Deaf/hard-of-hearing), each occurring as **Permanent**, **Temporary** (e.g., injury, vertigo from ear infection), or **Situational** (e.g., bright sunlight, noisy room, bumpy transit) constraints. Designing for permanent disabilities produces ergonomics that help all users (*curb-cut effect*).
- **Scenarios Framework `[Primer]`**: Audit and test against concise, **product-specific, UI-agnostic, task-oriented** scenarios (e.g., *"Approve a pull request"*, *"Filter the list of issues by project"*) rather than isolated pixels. Core scenarios receive end-to-end assistive technology (AT) audits and automated tests.
- **Target assistive technologies `[Primer]`**: Per WebAIM's Screen Reader User Survey #10 (Feb 2024) cited by Primer, primary desktop screen readers are **NVDA** (Windows), **JAWS** (Windows), and **VoiceOver** (macOS); mobile leaders are **VoiceOver** (iOS), **TalkBack** (Android), and **Commentary/Jieshuo** (Android). Never validate only on macOS VoiceOver and assume universal compatibility.

Sources: `https://primer.style/accessibility/foundations/accessibility-at-github/`, `.../accessibility-fundamentals/`, `.../scenarios-framework/`.

## Semantic HTML, ARIA, links vs. buttons, and naming

### Semantic structure and ARIA discipline `[Universal]`

- **Intrinsic metadata (`SC 4.1.2 Name, Role, Value`)**: Assistive technologies parse the accessibility tree computed from HTML semantics. Choose the right element (`<main>`, `<nav>`, `<header>`, `<footer>`, `<ul>`/`<ol>`, `<dialog>`, `<button>`, `<a>`, `<label>`) instead of styling `<div>`/`<span>` (`generic` role) to look interactive.
- **DOM order = reading order (`SC 1.3.2`)**: Assistive technology walks DOM source order regardless of CSS Flexbox/Grid/absolute visual reordering. Verify logical structure with styles stripped.
- **Landmarks restraint**: Use flat landmark hierarchies—at most one `<main>`, one top-level `<footer>`, and disambiguated `<nav>` landmarks (`aria-label="Primary"`, `aria-label="Conversations"`). Omit the word `"navigation"` or `"sidebar"` from `aria-label` because the landmark role announces it. Avoid deeply nested landmarks.
- **Headings hierarchy**: Use one `<h1>` per page describing the primary purpose (modal dialogs are an accepted exception with their own heading), step sequentially without skipping levels (`h1 -> h2 -> h3`), use CSS utilities/classes when visual size differs from semantic level, and add `.sr-only` headings where major regions are obvious only visually.
- **Redundant vs. forbidden ARIA**:
  - Do not add redundant explicit roles matching native HTML5 implicit roles (e.g., avoid `<footer role="contentinfo">` unless supporting legacy environments).
  - Never nest interactive elements inside other interactive elements (e.g., `<button>` inside `<a>` or click-card `<a>` wrapping action buttons)—render them as DOM siblings instead.
  - Never use `role="application"` on broad containers: it forces screen readers into focus mode and strips heading/landmark navigation shortcuts, requiring manual re-implementation of every key. Scope it strictly if needed on specialized sub-widgets (see Drag and Drop below).
  - Browsers silently tolerate illegal ARIA (e.g., `role="heading"` on `<img>`), hiding failures visually while breaking AT. Always check ARIA in HTML allowed-role rules and required child attributes (`aria-activedescendant`, `aria-controls`, `aria-owns`, `aria-posinset`, `aria-setsize`, `combobox`).

### Links vs. buttons and disabling rules `[Universal]`

| Criterion | Link (`<a href="...">`) | Button (`<button type="button\|submit">`) |
| --- | --- | --- |
| Purpose | Navigates to a resource, route, anchor, or download. | Triggers an action, toggles disclosure/dialog, or submits/modifies data. |
| Keyboard | `Enter` activates (`Space` scrolls page). | `Enter` and `Space` activate (even on styled "link-buttons" if coded as `<button>`, or vice versa match role behavior to element). |
| Visual signifiers | Underline inline body links, or maintain >=`3:1` contrast against surrounding text + >=`4.5:1` vs. background (or leading/trailing icon/shape; honor underline user preferences). Never use underline/italic/bold alone for non-link highlights. | Distinct padding and background/border distinction (`>=3:1` boundary, `>=4.5:1` label contrast), plus visible `:focus-visible` and `:hover` states. |
| New window (`target="_blank"`) | Avoid unless essential (e.g., preventing unsaved form loss). Preserves Back history and avoids unannounced context shifts. When used, include a **visible and programmatic cue inside the link** (preferred: visible `"… (opens in a new tab)"`; fallback: dedicated icon + `.sr-only` text; last resort: `.sr-only`). Note: an "external link" icon alone indicates destination, not new-tab behavior. | N/A |
| Disabling | **Never disable links** or strip `href` dynamically; rethink the UI flow instead. | Avoid `disabled` attribute (`<button disabled>` disappears from sequential keyboard Tab order and breaks tooltips). Hide unavailable admin/contextual controls instead; leave form submit buttons enabled and use validation feedback. If disabled appearance is unavoidable while immediately unlockable or explanatory, use `[Primer]` `inactive` / `aria-disabled="true"` alongside visible/programmatic explanation. |

**Inactive vs. Disabled distinction `[Primer]`**: In Primer (`Button`, `ActionMenu`), if an inactive control still opens an explanatory dialog when activated, do **not** mark it `aria-disabled="true"`; convey its status in visible text / `inactiveText`. Reserve `aria-disabled="true"` for non-activating loading or gated controls that must remain focusable (never drop focus by removing a loading button from the DOM; emit `"Saving..."` via a pre-mounted `aria-live="polite"` region).

### Accessible naming and images `[Universal]`

- **Name source priority**:
  1. Visible text inside the control (`<button>Save draft</button>`) — best for sighted, AT, and voice users.
  2. Partially/fully visually hidden `.sr-only` text inside the control (`Get started<span class="sr-only"> with a bug report</span>`—keep the leading space inside or between nodes so words do not concatenate as `"getstarted"`).
  3. `aria-labelledby` pointing to visible DOM text.
  4. `aria-label` (invisible to sighted users; easy to drift out of sync).
- **Critical rules**:
  - **Voice control (`SC 2.5.3 Label in Name`)**: Whenever `aria-label` overrides visible text, the accessible name **must start with or contain the exact visible text** (e.g., visible `"Learn more"`, `aria-label="Learn more about GitHub pricing plans"`, never `aria-label="Visit France"` for visible `"Learn more"`).
  - **Uniqueness & Verb-Noun clarity**: Avoid duplicate generic names like ten separate `"Edit"` or `"Remove"` buttons; include item context (`aria-label="Remove 'Apples'"` or `"Reply to benjiallen, 2:45PM on November 19"`). Buttons should follow `"Verb noun"` (e.g., `"Submit comment"`, `"Delete repository"`); links should stand alone in an AT link list (`"GitHub Enterprise pricing"`, not `"Click here"`).
  - **No redundant/patronizing hints**: Omit `"Click to go to..."`, `"Link to..."`, `"Use arrow keys..."`, or `"tab"`/`"button"` inside the name—roles and interaction hints come from semantics. Also omit redundant `aria-label` identical to visible text, and avoid hardcoding dynamic state strings (`"(currently viewing)"`, `"expanded"`) inside `aria-label`; use `aria-current="page"`, `aria-expanded`, or `aria-pressed`.
  - **Never use HTML `title`**: Inaccessible to touch and keyboard users and can clobber computed accessible names.
- **Images and icons (`SC 1.1.1`)**:
  - Every `<img>` must have an `alt` attribute: concise purposeful description for informative images (omit `"image of"` / `"button"`), empty `alt=""` for decorative images (never `aria-hidden` alone on `<img>`), and include important text embedded inside textual images.
  - Inline `<svg>` decorative icons inside labeled buttons/links must carry `aria-hidden="true"` and `focusable="false"`. Standalone meaningful icons (`Octicon`) expose `role="img"` + `aria-label` and require `>=3:1` contrast against their background.

Sources: `https://primer.style/accessibility/design-guidance/semantic-html-and-aria/`, `.../links-and-buttons/`, `.../alternative-text-for-images/`, `.../patterns/primer-components/descriptive-buttons/`, `.../headings/`, `.../icons/`, `.../links/`.

## Focus management, keyboard navigation, and shortcuts

### Focus lifecycle & indicators `[Universal]`

- **Visible focus (`SC 2.4.7`, `2.4.11`)**: Never strip `outline` without providing a compliant replacement (`>=3:1` contrast against background, not obscured by sticky headers/footers). Prefer `:focus-visible` so keyboard/switch/voice users see strong rings without distracting mouse clicks.
- **Dialog / modal focus (`focusTrap`)**: On open, move focus inside the dialog (default: first focusable item or close `'X'` button; command palettes may intentionally target an `<input>` inside; `ConfirmationDialog` focuses **Cancel** when `confirmButtonType="danger"` and **Confirm** otherwise). While open, trap circular `Tab`/`Shift+Tab` focus (`@primer/behaviors` `focusTrap` supports nested suspension stacks) and make background content `inert` + non-scrollable. `Esc` and backdrop clicks dismiss and return focus to the trigger control (or handle focus explicitly if the trigger no longer exists).
- **Dynamic DOM mutations (`focus order`)**:
  - **Adding items** (e.g., *"Load more"* comments, new AI chat thread): move focus to the first newly added item or its primary input if logical.
  - **Removing items**: do **not** let focus fall back to `<body>`; move focus programmatically to the previous interactive sibling, next item's menu/heading, or a primary composer field.
  - **Filtering**: keep focus on the active filter checkbox/menu trigger as results update so users can toggle multiple filters without interruption; restore focus logically only if a self-removing *"Clear filters"* control disappears on click.
  - **Tab vs. Arrow (`focusZone`)**: Standalone widgets use `Tab`/`Shift+Tab`; composite widgets (menus, tabs, trees, toolbars) expose a single/clean tab-stop and use `@primer/behaviors` `focusZone` with arrow keys (`ArrowVertical`/`ArrowHorizontal`, `Home`/`End`, optional wrapping or `"stop"`). Choose between real DOM `.focus()` (default) and `aria-activedescendant` (keeps DOM focus on an `<input>`, e.g., `SelectPanel`/combobox).

### Keyboard shortcuts (`SC 2.1.1`, `2.1.4`) `[Universal]`

- **Always additive**: Every shortcut-bound action must also remain reachable via normal focusable controls and `Enter`/`Space`.
- **Suppress accidental activation**: Avoid un-modified single-character shortcuts (`r`, `j`, etc.) that misfire for speech-input users or collide with screen-reader browse-mode commands (`h`, `b`, `k`). Under WCAG 2.1.4, any single-character shortcut **must** either (a) be turn-off-able (GitHub provides a global **Character key shortcuts** setting), (b) remappable to include a modifier (`Ctrl`, `Alt`, `⌘`), or (c) active only when its owning component holds focus.
- **Avoid reserved collisions**: Never override browser/OS chords (`Ctrl/⌘ + W, T, N, F, L`) or AT navigation keys; test against NVDA, JAWS, and VoiceOver.
- **Dual discovery & implementation**: Pair visible `<kbd>` hints + a `?` shortcuts help dialog (which updates when single-key shortcuts are disabled) with `aria-keyshortcuts`. Note the syntax pitfall `[Primer]`: `aria-keyshortcuts` expects WAI-ARIA format (`"Control+K Meta+K"`), whereas `github/hotkey`'s `data-hotkey` uses `"Mod+k"`—never copy the same string into both attributes.

### Target size & pointer rules (`SC 2.5.1`, `2.5.2`, `2.5.7`, `2.5.8`) `[Universal]`

- **Minimum web target**: `24×24 CSS px` (`Button` medium/large satisfy this; small `Button` with a 1-char label like `"i"` drops to `21.5px` width unless padded/widened). On coarse pointers/touch, expand hit targets (`ActionBar` / `TreeView` chevrons expand hit area to `44×44 px` with `12px` spacing gaps). Native mobile targets require `44×44 pt` (iOS) / `48×48 dp` (Android) with `>=8 pt/dp` separation.
- **Pointer safety**: Trigger activation on up-event (pointer cancellation `SC 2.5.2`), provide single-tap/keyboard equivalents for all multi-touch/swipe/path/drag gestures (`SC 2.5.1`, `2.5.7`), and provide disableable buttons for device-motion features (`SC 2.5.4`).

Sources: `https://primer.style/accessibility/design-guidance/focus-management/`, `.../patterns/keyboard-shortcuts/`, `.../tools-and-resources/checklists/engineering-checklist/`, `primer/behaviors` `docs/{focus-trap,focus-zone,anchored-position}.md`.

## App-like experiences, reflow, motion, and live regions

### SPA routing & soft navigations `[Universal]`

- Client-side SPAs forfeit browser tab refresh announcements. On every soft route change:
  1. Update document `<title>` immediately to match view purpose (`"View · Scope · Product"`).
  2. Dynamically move focus—prefer the closest relevant heading (`tabindex="-1"`, one of the rare sanctioned uses of focusing a non-interactive element) so screen readers and magnifiers orient to the new content. Focus an interactive control only when the task demands immediate typing/action.
  3. Clear stale queued announcements (`liveRegion.clear()`).
  4. Render a consistent `<footer>` landmark to mark content end cleanly across routes.
  5. Support both portrait and landscape orientations (`SC 1.3.4`; never lock unless essential).

### Text resize, spacing, and reflow `[Universal]`

- Support **50–400% zoom** (minimum **200% text-only & page zoom** readable without overlap/clipping, `SC 1.4.4`) and full functionality at **`320×256` CSS px** without two-dimensional scrolling (`SC 1.4.10`).
- Support custom user style overrides (`SC 1.4.12`): line-height `1.5x`, paragraph spacing `2x`, letter spacing `0.12x`, word spacing `0.16x` of font size.
- Use `rem`/`em` typography and intrinsic flex/grid growth; never lock text containers to fixed pixel `height`, and never write `user-scalable=no` in viewport meta tags.

### Motion and vestibular/epilepsy safety `[Universal]`

- Classify motion by intent (**Feedback**, **Editorial**, **Design**); scrutinize purely decorative "Design" motion first and eliminate it when unnecessary.
- **5-second cap / pause control (`SC 2.2.2`)**: Micro-animations without pause controls must stop within **5 seconds** and rest cleanly; any motion >5s must be opt-in or expose an adjacent **Pause/Stop** control. Never autoplay audio/video/carousels.
- **Hard seizure safety (`SC 2.3.1`)**: Never flash >3 times in any 1-second period, keep any flash area small (`<~341×256px`), and avoid saturated red flashes.
- **Reduced motion & alternatives**: Wrap non-essential CSS motion in `@media (prefers-reduced-motion: no-preference)`, check `window.matchMedia('(prefers-reduced-motion: reduce)')` in JS (`scrollIntoView({ behavior: 'instant' })`), prefer opt-in toggles where feasible (many users don't know OS settings exist), and pair informative animations with visible or disclosure-toggled text alternatives.

### Live regions & status announcements `[Universal]` / `[Primer]`

- **When to announce**:
  - **Always**: Route/location changes and failed user actions (validation/outages).
  - **Case-by-case**: Asynchronous background updates (`aria-live` on streaming CI logs: yes; noisy bystander presence indicators: avoid).
  - **Debounce rapid updates** and suppress spinner announcements for quick loads `<750ms`.
- **`@primer/live-region-element` contract `[Primer]`**: Mount **one global `<live-region>` element in the initial HTML payload** containing distinct `#polite` (`aria-live="polite" aria-atomic="true"`) and `#assertive` (`aria-live="assertive" aria-atomic="true"`) nodes; never inject live regions dynamically at announcement time (AT often misses them). **Caveat**: If a modal `<Dialog>` is open (`aria-modal="true"`), place the live region inside the dialog or external live regions may be silenced by screen readers.

Sources: `https://primer.style/accessibility/design-guidance/app-like-web-experiences/`, `.../motion-and-animation/`, `.../text-resize-and-spacing/`, `.../patterns/accessible-notifications-and-messages/`, `primer/live-region-element` `README.md`, `.../components/dialog/accessibility/`.

## High-risk patterns: toasts, tooltips, placeholders, truncation, and drag-and-drop

| Pattern | Why Primer Restricts It | Required Alternative / Safe Usage |
| --- | --- | --- |
| **Toasts** (auto-dismissing corner popups) | Violates or risks `SC 2.2.1` Timing Adjustable, `1.3.2` DOM sequence disconnect (rendered far at end of `<body>`), `2.1.1` keyboard dismiss/return focus, `4.1.3` status disruptions; missed on large monitors and screen magnifiers; obscures bottom CTAs; `Esc` collides with modals; lost from working memory. | Simple actions (creating an issue): let the newly rendered item speak for itself. Complex/bulk/async completions or errors: persistent `Banner` (move focus if blocking/actionable, live region if passive success), `InlineMessage`, confirmation page, `Dialog` for session/sync blocks, or inbox/email notifications. |
| **Tooltips** | Invisible until hovered/focused, unavailable on mobile touch and screen-reader browse mode, hides critical info. Hover/focus popovers must obey `SC 1.4.13` (Dismissible via `Esc`, Hoverable pointer over tooltip, Persistent until dismissed). | **Reserve `Tooltip` primarily as the visible label for `IconButton`** (rendered via hidden text + `aria-labelledby`), or brief non-essential `aria-describedby` hints on interactive controls. **Never** attach to `div`/`span`/`p` or `disabled` elements. Never repeat an already-visible button label. Prefer inline captions, disclosure hints, or visible text. |
| **Placeholders** | Styled too faint for `4.5:1` contrast (darkening looks pre-filled); vanishes on focus/typing (hurts memory/cognitive disabilities); clipped by narrow input widths; cannot hold interactive links; falls into fragile fallback accname. | Always pair inputs with a visible `<label for>` above the field and persistent `<FormControl.Caption>` below for hints, formatting, and help links. |
| **Char limits (`minlength`/`maxlength`)** | Screen readers do not announce `maxlength` enforcement, silently blocking typing. | Display explicit visible/programmatically associated character guidance (`aria-describedby`) plus live-region warning on limit. |
| **Truncation (`…`)** | Confuses screen readers (full DOM string vs. clipped visual) and risks obscuring critical distinctions or even inverting legal/semantic meaning (*"agree to all..."*); truncated focusable links split focus rings or trap AT. | Wrap text or allocate adequate container space before truncating. **Never** truncate text containing interactive links/buttons/mentions. If user-generated content must truncate, keep behavior consistent across breakpoints, preserve leading distinguishing prefixes, and offer expand/modal/"Read more" controls rather than hover-only tooltips. |
| **1D Drag & Drop** | Pointer dragging excludes keyboard, voice, switch, and single-tap users (`SC 2.5.7`). NVDA intercepts `Enter`/`Space` and emits `mousedown`/`mouseup`, breaking naive keyboard/mouse distinction. | Provide multiple paths: (1) Keyboard drag trigger button (split from an invisible pointer-only drag overlay `<div>` to bypass the NVDA quirk) that dynamically applies scoped `role="application"` **only while active**, uses `ArrowUp`/`ArrowDown` to move, `Enter`/`Space` to place, `Esc` to cancel, debounced `100ms` `aria-live="assertive"` announcements (`"moved between X and Y"`), plus explicit visual slots; (2) An accessible Reorder Dialog fallback (with live preview) for long lists/voice/mobile; (3) First-time instruction dialog with `"Don't show me this again"`. |

Sources: `https://primer.style/accessibility/patterns/accessible-notifications-and-messages/`, `.../tooltip-alternatives/`, `.../patterns/placeholders/`, `.../patterns/truncation/`, `.../patterns/drag-and-drop/`, `.../patterns/copilot-accessibility-practices/`.

## Component ARIA and keyboard quick reference

Verified from live Primer component accessibility specs (`@primer/react` / Primer Product Docs):

| Component | Semantics & ARIA Wiring | Keyboard & Focus Contract | Critical Implementation Rules |
| --- | --- | --- | --- |
| **ActionBar** | `role="toolbar"` by default (override role if nested in list rows using vertical arrows). | Single tab stop; `ArrowLeft`/`Right` cycle (wrapping), `Home`/`End`, `Enter`/`Space`. | Enlarge coarse/touch targets to `44×44 px` with `>=12px` gaps; overflow into menu. |
| **ActionMenu** | Trigger `aria-expanded` / popup; menu `role="menu"`, `menuitem`/`menuitemcheckbox`/`menuitemradio` + `aria-checked`; grouped sections `role="group"` + label. | Trigger: `Enter`/`Space`/Arrows open. Menu: `ArrowUp`/`Down` (cycle), `ArrowLeft`/`Right` (submenus), `Home`/`End`/`PageUp`/`PageDown`, letter typeahead mnemonics, `Esc` closes, `Tab` exits & closes. | Opening focuses first item; closing returns focus to trigger. `inactive` items stay focusable and take `inactiveText` (don't blindly use `aria-disabled` if interactive). |
| **Banner** | `<section>` landmark named by banner type + default `<h2>` (overridable/hideable), status icon `aria-hidden="true"`. | Action/dismiss controls `>=24×24 px`. Dismiss moves focus logically. | Non-essential success/info -> announce via live region without stealing focus; blocking/actionable errors -> move focus to Banner/heading. |
| **ConfirmationDialog** | `role="alertdialog"`, `aria-modal="true"`, `aria-labelledby` (title), `aria-describedby` (body). | `Tab`/`Shift+Tab` between Cancel & Confirm, `Enter` activates, `Esc` cancels. | **Smart initial focus**: focuses **Cancel** for `confirmButtonType="danger"`, **Confirm** for non-dangerous actions. Avoid `"Are you sure?"` / `"Yes/No"`; name exact impact & verb. |
| **DataTable** | `table`, `rowgroup`, `row`, `columnheader`, `rowheader`, `cell`; `aria-sort` on sortable cols; `aria-describedby` subtitle; `aria-current="true"` active page. | Interactive cells/actions/pagination focusable (`button`, not `a`, for client data pagination); horizontal scroll container focusable for arrow scrolling at `320px`. | Disambiguate repeated row actions by prepending row header/short ID (`"Actions: Issue #42"`). Live region announces pagination changes and >`750ms` loading/sorting. |
| **Dialog** | `<dialog>` (Rails) or `<div role="dialog">` (React) + `aria-modal="true"`, `aria-labelledby` pointing to `<h1>` title. | Focus moves to first interactive item (close `'X'` or command `<input>`); circular focus trap; `inert` backdrop; `Esc` / backdrop closes and restores trigger focus. | Always include visible title and `'X'` close button. Place live regions **inside** dialog when dialog is open. |
| **FormControl** | `<label for={id}>`, `aria-describedby` linking validation message + caption `<span>`s, `aria-invalid="true"` on error. | `Tab` to input; visible label sits above input. | Use `<FormControl.Caption>` instead of placeholder for critical hints; support autocomplete (`SC 1.3.5`) and prevent redundant entry (`SC 3.3.7`). |
| **NavList** | `<nav>` labeled via `NavList.Heading`, `<ul>`/`<li>`/`<a>`, `aria-current="page"`, subnav toggle `<button aria-expanded aria-controls>`. | `Tab` and `Enter` through links; *"Show more"* moves focus to first newly revealed link. | Never nest interactive controls inside `<a>` links; icons in `LeadingVisual` are decorative only. |
| **SegmentedControl** | `<ul>`/`<li>` containing `<button>` (same-view change) or link (navigation) with `aria-current`. **Do NOT** mark `tablist`, `radiogroup`, or `toolbar`. | `Tab` traverses each segment independently; `ArrowLeft`/`ArrowRight` do **not** move focus/selection; `Enter`/`Space` selects. | Immediate-effect single choice (no Save button); keep accessible group name and short noun labels. |
| **SelectPanel** | Trigger `<button aria-haspopup="dialog">` (label + selected value in accname); panel `role="dialog"`, filter `<input>` + virtual-focus list + live region count updates. | Opens -> real DOM focus on filter `<input>`, virtual focus on first option (`ArrowUp`/`Down` wrap, `Home`/`End`, `Enter` selects/toggles, `Tab` reaches footer buttons, `Esc` applies & closes -> trigger). | Selected value must remain part of trigger accname (avoid external label overriding value). Reflows cleanly at `320×256` CSS px. |
| **ToggleSwitch** | Native `<button aria-pressed="true\|false">`, `aria-labelledby` (visible neutral text label) + `aria-describedby` (caption). | `Tab` focus; `Space` (and optionally `Enter`) toggles **immediately** without Save. | `loading` spinner prop is visual-only—wire a live region announcement (`"Saving..."`) separately if async. |
| **Tooltip** | Label mode (hidden text + `aria-labelledby` on icon trigger) or Description mode (`aria-describedby`, `aria-hidden="true"`). | Opens on focus or hover; `Esc` dismisses without moving focus/pointer; stays open while hovered (`SC 1.4.13`). | Plain strings only; `IconButton` primary use; never on static tags or `disabled` controls. |
| **TreeView** | Tree semantics + accessible name via `aria-labelledby` (prefer heading, optionally `sr-only`). | Single tab stop (`focusIn` to first or last-focused node); `ArrowUp`/`Down` visible nodes, `ArrowRight` expand/first child, `ArrowLeft` collapse/parent, `Home`/`End`, `300ms` typeahead; `Space` reserved. | Moving focus on activation must preserve easy return path to tree; consistent leading visuals across siblings. |
| **UnderlineNav vs. UnderlinePanels** | **Nav**: `<nav aria-label>` + `<ul>`/`<li>`/`<a aria-current="page\|location">` (overflow menu `<button aria-expanded>`). **Panels**: `role="tablist"` (`aria-label`/`labelledby`), `tab` (`aria-selected`), `tabpanel`. | **Nav**: `Tab` + `Enter` across links. **Panels**: `Tab` jumps active tab -> active tabpanel; `ArrowLeft`/`Right` auto-activates tabs (`Home`/`End` jump). | **Never** confuse navigation (`UnderlineNav` URL changes) with in-page view switching (`UnderlinePanels` tabs). Active tab bold + higher contrast because coral underline alone fails 3:1 non-text contrast; avoid `UnderlinePanels` `loadingCounters` until AT text alternative lands. |

Sources: `https://primer.style/product/components/{action-bar,action-menu,banner,button,confirmation-dialog,data-table,dialog,form-control,nav-list,segmented-control,select-panel,toggle-switch,tooltip,tree-view,underline-nav,underline-panels}/accessibility/`.

## Generative AI and CLI accessibility extensions

### Copilot Accessibility Principles & Web AI UI `[Primer]`

Designing multi-turn and multi-agent AI products adds unpredictability; Primer defines five AI accessibility principles paired with concrete web rules:

1. **Clear & understandable**: Plain prose, structured markdown headings/lists inside responses, default essential details with expandable depth, multimodal text equivalents. Never rely on color alone for model/error states (`>=4.5:1` text, `>=3:1` graphics).
2. **Transparent & predictable**: Clearly distinguish **user prompts**, **AI replies**, **internal reasoning/thought trace**, and **generated artifacts**, as well as **individual agent identities and handoffs**. Confirm accessibility setting changes explicitly.
3. **Adaptable to user pace & style**: Let users control detail, format, output pacing, and **autonomy level**; avoid response/review time limits; consolidate accessibility preferences in one discoverable place and surface them in onboarding alongside *"icebreaker"* prompt lists (`<ul>/<li>/<button>`).
4. **Flexible & forgiving**: Preserve full conversation/artifact history, allow undo/edit/rollback, make diffs obvious, enable safe low-risk experimentation, and allow session sharing.
5. **Consistent & reliable**: Flat landmark layout (`main`, `complementary`, distinct named `nav`s), sequential headings inside workspace chrome and AI output (`sr-only` sidebar section headers like *"Quick links"*), state communicated via `aria-current`/`aria-expanded` attributes (not hardcoded into `aria-label`), focus managed cleanly when threads/responses are added or deleted, **periodic `polite` live announcements (~every 5s)** during long async generation (stopped upon completion), and immediate live announcements for stream timeouts, rate limits, or safety rejections.

### CLI Accessibility Foundations `[Primer]`

Text-first terminal UIs are not accessible by default across diverse shells, screen readers, and custom themes:

- **Variable environment & theme safety**: Never require TrueColor, specific Nerd/Unicode glyphs, mouse input, or fixed pixel/column sizes. Respect [`NO_COLOR`](https://no-color.org/) and document styling/animation opt-outs; derive palette carefully against configured ANSI themes rather than assuming black/white backgrounds, while bounding palette-query timeouts so output never blocks. Reflow by character columns (e.g., stacked labels `<80` cols, compact tables `80–119` cols, full tables `>=120` cols) with an untruncated inspection mode.
- **Stream-first semantics**: State status explicitly in text (`Success:`, `Warning:`, `Error:`) paired with recovery actions—never bare checkmarks/red crosses or spinner-only activity indicators. Do not use ANSI cursor movement to overwrite errors that leave no persistent final summary (`Summary:`, `Next steps:`). Offer plain-text fallbacks (`label + URL`, text alt + file path) for kitty images or OSC 8 hyperlinks.
- **Keyboard, noninteractive & safety parity**: Every interactive prompt must accept noninteractive flags/args/stdin (e.g., `--title`), except security/permission gates never bypassed silently. Support `Ctrl+C`, never spawn browsers/editors/pagers silently, preserve non-secret form values on validation error (never echo/log secrets or advise CLI-arg secrets), return consistent nonzero exit codes, report partial successes & cancellation ambiguity honestly (*"Cancelled: 2 of 5 uploaded... Uploaded files were not removed."*), and keep `--output json` machine output separate from human prose (stable schema, zero ANSI/spinners/truncation).

Sources: `https://primer.style/accessibility/foundations/copilot-principles/`, `.../patterns/copilot-accessibility-practices/`, `.../foundations/cli-accessibility/`.

## Design annotations, native mobile, and verification workflows

- **Design-to-code annotations (`Annotation Toolkit`) `[Primer]`**: GitHub's May 2024 audit analysis attributed **48% of audit issues to preventable design gaps (40% preventable via design annotations)**. Prioritize annotations with the **Tiered Model**:
  - **Tier 1 (Mandatory)**: Button, Form element, Heading, Image, Link, Page Structure.
  - **Tier 2 (Ideal)**: Focus order, Landmark, Language, List, Page Title, Primer A11y Presets, Video/Audio.
  - **Tier 3 (Nice to have / Advanced)**: Complex arrow stops & custom reading order, Keyboard shortcuts, System feedback, Live regions, Tables, User interactions/gestures, Mobile annotations.
  - Remember: annotations communicate *intent at handoff time*—they are not a permanent runtime source of truth.
- **Native mobile divergence (iOS & Android) `[Universal]`**: Mobile patterns reverse web link/button convention—use **buttons** for in-app navigation and **links** only for external browser/WebView destinations. Enforce `44×44 pt` (iOS) / `48×48 dp` (Android) targets (`>=8 pt/dp` spacing), support Dynamic Type / font scaling, support iOS VoiceOver two-finger Z-scrub dismissal on custom overlays, match spoken Voice Control / Voice Access labels (plus `.accessibilityInputLabels` aliases if needed), hide decorative views (`accessibilityHidden(true)` / `importantForAccessibility="no"`), avoid orientation locks (`SC 1.3.4`), and apply the web Designer/Engineering checklists inside WebViews (`View Context` handoff).
- **Verification stack `[Universal]`**: Pair static checks (**axe DevTools**, **eslint-plugin-jsx-a11y**, **GitHub Accessibility Scanner Action**, **Nu HTML Validator**, **Microsoft Accessibility Insights**, **HeadingsMap**, **WebAIM Contrast Checker**) with mandatory manual checks: full keyboard-only traversal (`Tab`, `Shift+Tab`, `Arrows`, `Enter`, `Space`, `Esc`), grayscale view test, `200%`/`400%` zoom & `320×256` reflow, WCAG `1.4.12` text-spacing bookmarklet, Windows High Contrast (`forced-colors`), `prefers-reduced-motion`, and real screen reader runs across NVDA/JAWS/VoiceOver.

Sources: `https://primer.style/accessibility/annotation-toolkit/`, `.../deep-dives/tiered-model/`, `.../deep-dives/annotation-theory/`, `.../tools-and-resources/checklists/{designer,engineering,mobile}-checklist/`, `.../extensions-and-tools/`.

## Agent application notes

- **Framework portability (`[Universal]` vs. `[Primer]`)**: When building non-Primer React, Vue, Svelte, Tailwind, or vanilla apps, enforce every `[Universal]` contract above—semantic HTML, link-vs-button separation, `:focus-visible`, `focusTrap`/`inert`, single global live region, ban on auto-dismissing toasts for critical errors, `24×24` minimum targets, and `320×256` reflow—using native `<dialog>`/popover primitives or accessible headless libraries (e.g., React Aria, Radix, Ark) rather than forcing `@primer/react` or `@primer/behaviors`.
- **When implementing `[Primer]`**: Compose `@primer/react` / `@primer/behaviors` / `@primer/live-region-element` directly so built-in keyboard zones, `aria-modal` trapping, and live-region timing come out of the box—then supply the integration-level props Primer cannot infer (`aria-label` on `NavList`/`UnderlineNav`, `aria-current`, unique item context on repeated action buttons, inside-dialog live regions when modals are active).

## Gaps, caveats and source issues

- **Internal GitHub references**: Links across Primer's accessibility docs point to staff-only repositories and docs (`github/accessibility`, `github/scenarios`, `github/design`, `github/primer`, `thehub.github.com`, `accessibility-playbook.github.com`, `#accessibility` Slack) that are inaccessible outside GitHub.
- **Component parity gaps disclosed by Primer**: Several component accessibility pages explicitly document known library limitations as of the 2026 live snapshot—e.g., `UnderlinePanels` `loadingCounters` currently lack AT text alternatives (`SC 1.1.1` failure—avoid using); `ActionMenu` `inactive` state is not yet announced programmatically unless paired with `inactiveText`; `SelectPanel` external visible `<FormControl>`-style labels can currently override the trigger button's selected value announcement, and grouped option headers are not yet exposed to screen readers; small `Button` padding drops below `24px` width on single-character labels.
- **Tooltip doc discrepancy**: `accessibility/patterns/primer-components/tooltips/` still links to `primer.style/view-components/components/alpha/tooltip`, whereas `product/components/tooltip/` covers current React/product guidance (and its own `accessibility/` page includes one staging URL typo `primer-docs-staging.githubapp.com`).
