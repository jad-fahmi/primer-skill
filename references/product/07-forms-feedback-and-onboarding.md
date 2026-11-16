# Forms, Validation, Feedback Messaging, and Feature Onboarding

> Synthesizes Primer's form layout and validation contracts, confirmation dialogs, notification messaging (`Banner`, `InlineMessage`, `Popover`), progressive disclosure icons, and timeboxed feature onboarding patterns.

## Contents

- [Key takeaways](#key-takeaways)
- [Form anatomy, input choice, and layout](#form-anatomy-input-choice-and-layout)
- [Validation timing, error recovery, and degraded forms](#validation-timing-error-recovery-and-degraded-forms)
- [Destructive confirmations and `ConfirmationDialog`](#destructive-confirmations-and-confirmationdialog)
- [Notification messaging, decision flowcharts, and `Popover` / `Details`](#notification-messaging-decision-flowcharts-and-popover--details)
- [Feature onboarding and product release lifecycles](#feature-onboarding-and-product-release-lifecycles)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- `[Universal]` **Forms favor vertical scanability and visible labels**: Stack `FormControl`s in a single vertical column, pair every field with a concise `<=3`-word sentence-case label above it, never replace labels with placeholders, and batch keyboard inputs together before pointer selections.
- `[Universal]` **Validate gently—submit first, then assist inline**: Suppress browser-native validation bubbles and avoid disabled submit buttons. Validate on submit by default; validate valid fields on blur only after modification, and validate on keystroke **only after** a field enters an invalid state so users get immediate confirmation when fixing mistakes.
- `[Universal]` **Suppress redundant alerts and replace toasts**: Never stack multiple page banners, never place alerts outside modals for in-dialog actions, and skip loud success banners when the UI mutation itself proves success (still notify assistive technology).
- `[Primer]` **Timebox every onboarding nudge and match release stage language**: Cap active teaching UIs at `<=2` per view, prevent per-repo announcement fatigue, graduate/retire previews within 2 months, and use `Private preview` / `Public preview` / `General availability` (no longer `"Alpha"`/`"Beta"`).

## Form anatomy, input choice, and layout

### `FormControl` anatomy `[Universal]` / `[Primer]`

Every field follows four ordered regions wired programmatically via `id` / `for` / `aria-describedby`:

1. **Label (required)**: Concise (`<=3 words`), sentence case (*"Repository name"*, *"Payment method"*), sitting above text inputs (`horizontal` layout allowed for checkboxes/radios). Never use `placeholder` text as a label—it disappears on input, fails `4.5:1` contrast when dimmed (or mimics pre-filled data when darkened), clips inside narrow fields, and lacks accessible link support.
2. **Required indicator**: Visibly mark required fields and set native/ARIA `required` attributes—except unambiguous universal forms (e.g., sign-in) where all fields are implicitly required. Never mark an individual `Checkbox` or `Radio` as `required` (mark the parent group instead).
3. **Caption (optional)**: Short supplementary hint or formatting rule placed below the control via `FormControl.Caption`. Never duplicate what the label already says, and **hide a caption if an active error message duplicates its text** so validation errors stand out visually.
4. **Validation message (optional)**: Clear cause + fix guidance tied via `aria-describedby`. Place a single validation message at the **`CheckboxGroup` or `RadioGroup` level**, never on individual checkboxes/radios.

### Input selection guide `[Universal]` / `[Primer]`

| Data / Interaction Need | Recommended Control | Why & Boundary |
| --- | --- | --- |
| Open-ended free text (1 line / multi-line) | `TextInput` / `Textarea` | Size width conservatively to expected value length (or 100% container width for unbounded text); avoid artificial shrinking. |
| Free text + optional suggestions | `Autocomplete` | Lets users either pick a match or type a custom string. |
| Immediate toggle setting (binary, no Save button) | `ToggleSwitch` | Immediate persistence; pair with async live-region announcement if saving asynchronously. |
| Mutually exclusive short list (in a form) | `RadioGroup` | All choices stay visible at once; static widths (`Checkbox`/`Radio` never stretch). |
| Mutually exclusive short immediate setting | `SegmentedControl` | `2–5` labeled options applying instantly without Save. |
| Multi-select short list | `CheckboxGroup` | Visible comparisons with group-level validation. |
| Single select from longer list (in a form) | `Select` | Native/clean dropdown sized to longest option or parent container width. |
| Quick action commands or `<=~15` simple picks | `ActionMenu` | Never embed `<input>` filter boxes or form controls inside `ActionMenu`. |
| Searchable / async / multi-select picker from large lists | `SelectPanel` | Built-in filter `<input>` + virtual-focused `ActionList`. **Nonmodal**: saves immediately on close/`Esc`/outside click; **Modal / full-screen**: confirms on footer `Save` button, discards on `Cancel`/`'X'`/`Esc`. Pin initially selected items at the top **on open or filter-clear only**—never reorder mid-click. |

### Form structure & progressive disclosure `[Universal]`

- **Single vertical column by default**: Avoid multi-column grid forms built just to compress height—vertical rhythm minimizes eye-tracking errors.
- **Logical ordering**: Order fields by (1) importance, (2) semantic grouping (visual proximity for related settings), and (3) input modality (group typing fields together so keyboard/mouse users don't ping-pong hands repeatedly).
- **Progressively disclosed sub-controls**: Conditional controls revealed below a checked `Checkbox` or selected `Radio` may visually omit a redundant `<label>` **only** when the parent choice provides complete visual clarity—and **must still supply a visually hidden accessible label** (`visuallyHidden` on `FormControl.Label`). If the parent doesn't supply clear context, keep a visible non-clashing label.

Sources: `https://primer.style/product/ui-patterns/forms/`, `.../components/{form-control,select-panel,action-menu}/guidelines/`.

## Validation timing, error recovery, and degraded forms

### Validation rules `[Universal]`

- **Reject browser-native validation bubbles**: HTML5 constraint popups (`required`, `minlength`, `maxlength`) lack consistent AT compatibility and clash visually. Use `novalidate` patterns backed by Primer/custom inline validation.
- **Never disable form submit buttons**: Leaving submit enabled guarantees users can trigger clear error feedback showing what blocks completion.
- **Message semantics**:
  - **Error**: State *what* failed and *how to fix it* (no vague *"Invalid input"*).
  - **Success**: Reserve inline green field validation for genuinely uncertain server checks (e.g., *"Repository name is available"*).
- **When to validate**:
  1. **On submit (default)**: Let users fill fields smoothly without premature red error noise.
  2. **Switch to inline validation after first submit failure**, or when validating modified fields:
     - **Valid field**: Wait until the user edits the value **and blurs** focus away—never interrupt active typing on a clean field.
     - **Invalid field**: Validate on change/keystroke so positive feedback removes the error the instant input becomes valid.
  3. **Slow async checks (`>1s`)**: Display an inline loading indicator alongside/under the field.

### Error focus & DOM cleanup `[Universal]`

- **Do NOT use `aria-live` regions for form validation**: Moving focus reliably outperforms live regions here and avoids interrupting screen readers mid-typing.
- **`1–2` errors on submit**: Move focus to and scroll into view the **first invalid `<input>`**.
- **`>=3` errors on submit**: Render an **Interactive Summary of Errors `Banner`** at the top of the form containing jump links (`<a href="#field-id">`) that focus each invalid field on click, and **move focus directly into that Banner** (first link or heading) on submit failure.
- **DOM hygiene**: Mark invalid inputs with `aria-invalid="true"` + `aria-describedby={validationId}`; **remove `aria-describedby` pointing to the validation ID whenever the error clears from the DOM**.

### Degraded forms `[Universal]`

| Backend / Data State | UI Behavior |
| --- | --- |
| **Save failed / read-only outage** | **Preserve user input** and leave form visible (do not wipe or disable controls just to hide errors). Inform users the form is temporarily read-only/unsavable; if they retry submitting, show an explanatory error Dialog. |
| **All form data unreadable** | Remove the empty/broken form entirely and display an error/unavailable Banner or empty state explaining the outage. |
| **Partial field data unreadable** | Disable *only* the specific affected fields and show inline error messages directly below those fields so users know missing data stems from an outage rather than blank values. |

Sources: `https://primer.style/product/ui-patterns/forms/#validation`.

## Destructive confirmations and `ConfirmationDialog`

Use `ConfirmationDialog` (`role="alertdialog"`) for **irreversible, destructive, widespread, or data-losing actions** (deleting repos/accounts, publishing notifications, discarding unsaved edits). **Avoid** it for frequent low-risk actions (confirmation fatigue), undoable actions (prefer Undo), or multi-step workflows (use a dedicated page).

- **Copy formula (`Title`, `Body`, `Buttons`) `[Universal]`**:
  - **Title**: Specific `<5`-word sentence-case question naming action + target (*"Delete repository?"*, *"Discard changes?"* — **never** *"Are you sure?"* or bare *"Delete?"*).
  - **Body**: Name the exact resource(s) and irreversible impact (*"This will permanently delete the 'my-repo' repository, including all files, commit history, and issues. This action cannot be undone."*).
  - **Buttons**: Imperative `1–3`-word verb-noun primary CTA (*"Delete repository"*, *"Publish now"*) + clear cancel (*"Cancel"*, *"Keep as draft"*)—**never** generic *"Yes"/"No"* or *"OK"*.
- **Sizing & smart safety focus `[Primer]`**: Width presets **`small` (`296px`)**, **`medium` (`320px`, default)**, **`large` (`480px`)**. With `confirmButtonType="danger"`, focus opens safely on **Cancel**; for non-dangerous confirmations, focus opens on **Confirm**.

Sources: `https://primer.style/product/components/confirmation-dialog/{guidelines,accessibility}/`.

## Notification messaging, decision flowcharts, and `Popover` / `Details`

### Messaging tiers & semantic variants `[Primer]`

| Category | Origin & Dismissibility | Component Choice |
| --- | --- | --- |
| **System updates** | System-initiated (SSO warning, outage, compliance). Permanent or non-dismissible until fixed. | `Banner` |
| **Feedback** | Response to user action (`form submit`, `setting toggle`, `bulk create`). | `InlineMessage` (next to control/table cell) or `Banner` (top of section/body or dialog). Keep persistent for blocking/complex feedback; avoid toasts. |
| **Awareness** | Helpful context/tips not tied to an action. Dismissible (`onDismiss` stored persistently where useful). | `info` `Banner` (actionable prompt) or `InlineMessage` (lightweight tip). |

**Six states (`notification-messaging.md`) `[Primer]`**:
- `info` (blue): helpful context, SSO/account info (supports custom icon in `Banner`).
- `warning` (yellow): upcoming changes, partial outage, non-destructive side effects (billing/access shifts).
- `critical` (red): failed action, validation error, destructive warning (supports dismiss on failed action, **never dismissible** when warning about blocking unresolved errors).
- `success` (green): unexpected or delayed success—**use sparingly**! Omit visible banners when mutation itself proves success (e.g., saving an inline comment or redirecting to the newly created issue page still needs AT status notification, not redundant green chrome). Copy button -> inline green check or inline *"Issue #21 created"* link below bulk create button works better than page-top banners.
- `unavailable` (neutral/gray-alert): inline degraded UI rows (`DataTable` cells, missing permission blocks).
- `upsell` (purple): plan upgrade unlock prompts (supports custom icon in `Banner`).
- **Icon style parity rule `[Primer]`**: Icons sized at **`12px`** (e.g., compact inline messages) use **filled** variants; icons sized at **`16px`/`24px`** paired with `>=14px` body text use **outline** variants. Note: `InlineMessage` supports `critical`, `warning`, `success`, and `unavailable` variants (`medium` default & `small`).

### `Banner` layout & placement guardrails `[Primer]`

- **Max 1 Banner per page** (prevents vertical stacking and alert fatigue) and **max 2 actions** (`1` recommended; with `2`, pair `primary` + `secondary`).
- **Paragraph sizing only**: Never put oversized headings or mixed heading scales inside `Banner` copy (requires an accessible `<h2>` title, visually hidden via CSS if message body is brief and scannable).
- **Close-to-action placement**:
  - **Top of page/section body**: Maintain outer layout margins—never render full-bleed above global navigation.
  - **Inside `Dialog` / `SelectPanel`**: Render directly under dialog header when alerting about dialog contents—**never close a Dialog just to show a Banner at page top**.
  - **Flush variant**: Only inside tight containers (`Dialog`, `DataTable`, `Card`, `Box`); **never** flush at the top of a full page.
- **Responsive reflow**: Single-line desktop banners align dismiss `'X'` and actions inline; titled/complex or narrow banners stack actions left-aligned below text (dismiss `'X'` top-right).

### Error & success decision rules `[Primer]`

- **Success**: Result obvious in page? -> **No banner** (notify AT only). Hidden after save/submit? -> Redirected to different page without clear evidence -> top `Banner`; stayed on same page -> `InlineMessage` right next to Save button.
- **Error (known cause)**: Form/input error -> Form validation rules. Mouse drag-and-drop failure -> dismissible modal Dialog with recovery steps. Action inside a Dialog -> if quick or blocking, **keep Dialog open** and put `Banner` inside Dialog top; if long-running where user can work elsewhere on page -> live progress `Banner` updating to error/success; if user can navigate away -> app notifications tray. Otherwise -> `Banner` or `InlineMessage` nearest the trigger.
- **Error (unknown cause)**: Obvious on screen? -> Notify AT cleanly without vague alerts. Blocking main task? -> Dismissible error Dialog with next steps. Non-blocking -> `Banner` or `InlineMessage` near trigger (or top-level `Banner` if system-wide).

### `Popover` & progressive disclosure icon semantics `[Primer]`

- **`Popover` rules**: Always pointed (`caret` position configurable), dismissible (`onEscape` / `onClickOutside` + visible dismiss button), reserved for optional contextual onboarding/tips—**never display errors or essential info inside a `Popover`**, and **never** overlap multiple popovers simultaneously.
- **Progressive disclosure (`Details`) icon discipline**: Limit hiding content to genuine overload relief, preserve focus on trigger, and **never mix icon meanings**:
  - **Chevron (`up`/`down`)**: Collapsing/expanding vertical sections (pair with descriptive text like *"Show more"* when non-obvious). **Never** use chevrons for dropdown menus or pagination.
  - **Fold/Unfold**: Code-diff / profile timeline vertical expansion blocks; usually stands alone **without** verbose text.
  - **Ellipsis (`...`)**: Expanding inline truncated body text. **Never** confuse with **Kebab** (dropdown menu trigger) or **Caret** (dropdowns, not section disclosures). Avoid bare text-only toggles without visual/structural framing.

Sources: `https://primer.style/product/ui-patterns/{notification-messaging,progressive-disclosure}/`, `.../components/{banner,inline-message,popover,details}/`.

## Feature onboarding and product release lifecycles

In-product onboarding is a guided *"unboxing"* workflow—distinct from marketing sites or release emails.

### Onboarding guardrails `[Universal]` / `[Primer]`

1. **Proportionality & proximity**: Anchor callouts directly beside where the feature permanently lives, sized to its real importance—never drop a page-wide announcement banner for a minor tweak.
2. **Never trap or derail**: Provide effortless dismissal, and if an onboarding CTA leads away from an active workspace/form, open in a new tab/window or save context cleanly so unsaved work survives.
3. **Timebox & tally campaigns `[Primer]`**: Define explicit max calendar days **and** max per-user impressions; deduplicate across multi-org/multi-repo users so dismissing once doesn't replay on every repository visit.
4. **Traffic control**: **Maximum 2 onboarding alerts/nudges** visible simultaneously on any screen; coordinate across teams before landing.

### Onboarding patterns & copy `[Primer]`

- **3-part narrative**: **Beginning** (first-visit empty states or contextual callout -> invite entry) -> **Middle** (progress checklists, guided setup forms) -> **End** (concise completion banner acknowledging setup).
- **Teaching bubble (`Popover`)**: Show **1 at a time**, target visible interactive controls only, cap around **`~160` characters**, lead with a clear headline, use **`"OK, got it"`** (default single CTA) or **`"OK, dismiss"`** (when alongside a value CTA), and optionally add emojis/pictograms/GIFs for complex interactions.
- **Dashboard feature bulletin**: Top right of logged-in dashboard only; **`~160`-char** budget + explicit action button (no mystery links).
- **In-product branded empty state**: Permissible for major first-time surfaces (e.g., Actions landing), provided transitions back to normal product density once populated feel seamless.
- **Feature Preview Dialog**: Centralized opt-in modal under user menu; must include monitoring + graduation/removal plan and **never linger unmonitored beyond 2 months**.

### Release lifecycle labels (`feature-onboarding.md`) `[Primer]`

Replace legacy `"Alpha"` / `"Beta"` labels with Primer's four current stage terms (plus `"Technical preview"` for experimental research projects):

| Lifecycle Label | Definition & Support Expectations | UI Rules |
| --- | --- | --- |
| **`Private preview`** (formerly Alpha / Private Beta / Staff ship) | Invite/staff ship, unannounced, engineer-supported (no SLAs). | Use standardized preview `Label` treatment; never needs formal deprecation if retired before Public preview. |
| **`Public preview`** (formerly Public Beta) | Publicly announced; not SLA-backed production dependency. | Pair standalone preview labels (when outside main nav) with an adjacent **`Give feedback`** link to a discussion thread. Use official preview colors—don't improvise custom badge hues. |
| **`General availability` (GA)** | Broadly available with standard SLAs and support. | Remove preview badge chrome cleanly once promoted. |
| **`Closing down`** | Sunset / deprecation path for previously public features. | Pair with migration/docs links in advance. |

Sources: `https://primer.style/product/ui-patterns/feature-onboarding/`.

## Agent application notes

- `[Universal]` Apply single-column `FormControl` layouts, post-submit + post-error inline validation timing, interactive error-summary banners (`>=3` errors), read-only preserved degraded form state, `ConfirmationDialog` danger focus + specific copy rules, and toast avoidance across any stack (native HTML, Tailwind, MUI, React Hook Form, Rails).
- `[Primer]` Compose `@primer/react` `FormControl`, `SelectPanel`, `ConfirmationDialog` (`useConfirm()`), `Banner`, `InlineMessage` (experimental), `Popover`, and `Details` (`useDetails()`) rather than rebuilding custom overlays/validation chrome. When calling `<InlineMessage>` in React, note the docs typo `'unvailable'` vs rendered `'unavailable'` and verify TypeScript prop autocomplete in the installed version.

## Gaps, caveats and source issues

- **`InlineMessage` maturity & docs typo**: `@primer/react` lists `InlineMessage` as `experimental`, and its live props table (`primer.style/product/components/inline-message/`) contains a typo (`'unvailable'`) even though code examples use `variant="unavailable"`. Always verify against TypeScript definitions in `@primer/react/experimental`.
- **Missing standalone guideline subpages**: `FormControl`, `InlineMessage`, and `Details` lack `/guidelines/` routes on `primer.style/product/components/` (their behavioral rules live in `/ui-patterns/forms/`, `/ui-patterns/notification-messaging/`, and their overview/accessibility pages).
- **Onboarding cross-product drift**: Some example screenshots on `feature-onboarding/` show legacy `"Blank Slate"` and `"Flash"` terminology from older iterations of GitHub UI, even though the lifecycle section explicitly standardizes modern `Private preview` / `Public preview` labels and `Banner` patterns.
