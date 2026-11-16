# Brand Identity, Attributes, and Voice

> Defines how GitHub's marketing and brand identity is positioned separately from its product UI, including the five brand attributes, co-branding and Copilot policies, trademark boundaries, and access limits of the public voice guide.

## Contents

- [Key takeaways](#key-takeaways)
- [Brand positioning vs product identity](#brand-positioning-vs-product-identity)
- [The five brand attributes](#the-five-brand-attributes)
- [Voice, tone, and audience calibration](#voice-tone-and-audience-calibration)
- [Logos, wordmarks, and product lockups](#logos-wordmarks-and-product-lockups)
- [Copilot identity policy (2025–2026 updates)](#copilot-identity-policy-20252026-updates)
- [Co-branding and partner rules](#co-branding-and-partner-rules)
- [Trademark and copyright guardrails](#trademark-and-copyright-guardrails)
- [Agent application notes](#agent-application-notes)
- [Gaps, caveats and source issues](#gaps-caveats-and-source-issues)

## Key takeaways

- **[Universal]** A design system separates **marketing/brand expression** (storytelling, emotional resonance, campaign impact) from **product UI** (calm density, workflow efficiency, repeated tool familiarity). Do not import promotional theatre into daily workspaces or flatten marketing sites into cramped admin forms.
- **[Universal]** Express brand personality through tunable attributes across copy, visual design, and experiential surfaces—leading with 1–2 dominant attributes per context rather than cranking every dial to maximum at once.
- **[Brand]** GitHub's five official brand attributes are **Nerdy**, **Confident**, **Authentic**, **Imaginative**, and **Empathetic** (used to evaluate whether an asset feels "GitHubby").
- **[Brand]** GitHub is styled as one word with capitalized G and H (`GitHub`). The standalone Invertocat is reserved for GitHub-owned contexts; external or corporate settings use the Invertocat + wordmark lockup at highest available contrast (`white`, `black`, or limited `grey`/`green`).
- **[Brand]** Both the standalone Copilot hero logo (retired 2025) and the standalone Copilot mascot head (retired early 2026) are **deprecated**. Current Copilot branding uses the GitHub Core lockup and AI illustration motifs rather than retired marks.
- **[Brand]** `GITHUB®`, `INVERTOCAT`, and `OCTOCAT®` are registered trademarks and federally registered copyrights of GitHub, Inc. Never use GitHub marks as another product's logo, imply GitHub endorsement, or apply them to unapproved merchandise.

## Brand positioning vs product identity

GitHub maintains two distinct public design portals for a deliberate structural reason:

| Dimension | Brand Toolkit (`brand.github.com` + `@primer/react-brand`) | Primer Product (`primer.style/product` + `@primer/react`) |
| --- | --- | --- |
| Primary goal | Inspire, explain value, launch campaigns, convert visitors | Help developers inspect code, review PRs, ship work cleanly |
| Audience | Prospective buyers, decision-makers, event attendees, broader dev community | Active daily users inside repositories, issues, queues, settings |
| Aesthetic posture | Expressive, gridline/editorial framing, large display scale, deliberate storytelling motion | Calm, dense, high-contrast functional chrome, fast micro-interactions |
| Visual assets | Bespoke illustrations, dithering, contribution-graph wallpapers, mascots (Mona/Ducky), stylized UI screenshots | Octicons, functional avatars, real data tables, status labels |
| Creative entry test | 4 context questions: audience, expressive vs functional moment, digital/print/environmental, GitHub vs external context | Task frequency, information density, WCAG AA task completion |

Before creating brand collateral, the Brand Toolkit Getting Started guide asks creators to anchor four axes: target audience, whether the moment is expressive or functional, medium (digital, print, environmental), and GitHub-owned vs external context.

Sources: `https://brand.github.com/guides/getting-started/`, `https://brand.github.com/`.

## The five brand attributes

Every brand surface translates the same five core traits into copy, visual craft, and live/experiential programming. Rather than forcing all five equally onto every surface, lead with one or two that fit the moment:

| Attribute | Copy behavior | Design behavior | Experiential behavior |
| --- | --- | --- | --- |
| **Nerdy** | Precision, exact language/punctuation; embrace technical depth while keeping it approachable; amplify practitioners and hobbyists | Monospace accents, code-inspired details, strict mathematical grids, complex hierarchy made legible | Deep technical sessions, longer workshops, hands-on tools and real datasets |
| **Confident** | Authoritative clarity demonstrating genuine domain expertise; explain problems and fixes pragmatically | Vibrant detailed visuals anchored by bold headlines and disciplined detail; high-quality defaults | Deliver on stated commitments; authoritative speakers who answer hard technical questions |
| **Authentic** | Conversational, short, plainspoken sentences; cut hollow buzzwords and marketing fluff | Clean uncluttered compositions with generous whitespace; expressive mascots and narrative delight | Encourage peer networking and interactive demos; high craft without ostentatious flash |
| **Imaginative** | Aspirational, big-picture vision celebrating what builders can invent | Bold, convention-breaking key moments mixing abstraction with clear visual signifiers | Prototype-rich spaces sparking "what if" wonder and glimpses of the future |
| **Empathetic** | Optimize readability and accessibility; address the reader ("you" over "we") and help them succeed | Test contrast, reading context, and type scale relentlessly; clear cues and predictable layouts | Explicit wayfinding; multi-modal access (in-person, virtual, async); diverse voices |

*Editorial note*: The live Toolkit heading spells the fourth trait *"Imaginitive"* on the page slug anchor, normalized to standard spelling (*Imaginative*) in body descriptions unless referencing raw DOM text.

Sources: `https://brand.github.com/brand-identity/attributes/`.

## Voice, tone, and audience calibration

The public Brand Toolkit explicitly avoids publishing detailed grammar, channel-by-channel tone sliders, or editorial style tables on `brand.github.com`. Instead, the Voice and Tone section links to an internal/external-restricted GitHub document (*Voice & tone guide*) unavailable outside authenticated staff/partner access.

From the publicly visible attributes and component content rules, coding agents can verify only the following public voice boundaries:

1. **Plainspoken technical authority**: Prefer short, concrete sentences over inflated marketing superlatives (`Authentic` + `Confident`).
2. **Reader-centered framing**: Speak to what the developer can build or solve (`"you"` more than `"we"`) rather than self-congratulatory corporate claims (`Empathetic`).
3. **Respect exact capitalization**: Always spell `GitHub` as one word with capital `G` and `H`. Do not split or lowercase (`Github`, `Git Hub`).
4. **Concise slot discipline**: Brand React guidelines enforce tight copy bounds (for example `<= 160` characters for standard `River` body copy, `<= 2` lines for `Hero` headings, single-line button labels in sentence case).
5. **No placeholders in production**: Never leave `Lorem ipsum`, draft filler, or vague placeholder copy in generated pages (`@primer/brand-mcp` explicitly flags `lorem ipsum` as a review error).

For universal UI microcopy rules that *are* fully public (7th-grade reading level, plain verbs, sentence case, accessible link names), see [Iconography and Content UX](../product/10-iconography-and-content-ux.md).

Sources: `https://brand.github.com/brand-identity/voice-and-tone/`, `https://brand.github.com/brand-identity/attributes/`, `https://brand.github.com/foundations/logo/`, `primer/brand` (`packages/mcp/src/review/rules.ts`).

## Logos, wordmarks, and product lockups

GitHub distinguishes four brand-mark tiers:

1. **Invertocat (primary pictogram)**: Represents the Octocat. May appear standalone *only* inside GitHub-owned environments or contexts where GitHub attribution is unmistakable (logged-in product chrome, GitHub email headers, docs, attendee badges at official GitHub events).
2. **Wordmark lockup (Invertocat + wordmark)**: Default choice for corporate, external, and third-party partnership contexts where immediate name recognition matters.
3. **Product lockups (e.g., `GitHub Copilot`, `GitHub Security`)**: Pair the full product name with the GitHub mark when a sub-product needs independent identity while staying anchored to GitHub. Inside `github.com`, where the parent platform is already obvious, navigation lockups may omit the Invertocat.
4. **Event and initiative sub-brands (e.g., `GitHub Universe`, `The ReadME Project`)**: Time-bound flagship events or editorial publications receive custom expressive visual identities that must **not** be backported as default core brand rules.

### Logo usage rules

- **Approved colors**: Render the Invertocat and wordmark only in pure white, black, or limited gray/green treatments—always choosing the highest WCAG-compliant contrast option against the underlying surface.
- **Hierarchy and legibility**: Place the logo high in visual hierarchy with generous negative space and zero competing visual noise behind it.
- **Hard prohibitions**: Never rearrange logo geometry, replace the logo with Mona or mascot illustrations, add drop shadows/gradients/glows, place marks over busy photography/textures, mix colors inside the lockup, or stretch/skew/crop the artwork.

Sources: `https://brand.github.com/foundations/logo/`.

## Copilot identity policy (2025–2026 updates)

GitHub retired its standalone Copilot branding marks in two documented stages:

- **2025**: The standalone **Copilot logo** (previously heroing the Copilot goggle icon as a separate brand mark) was **deprecated**.
- **Early 2026**: The **Copilot mascot head** was permanently **retired** from GitHub's visual identity. Agents and designers must **not** use either new or archival Copilot mascot head artwork in any generated UI, marketing asset, or mockup.
- **Current treatment**: Copilot uses product lockups tied to the core GitHub brand (Invertocat primary mark) and, in editorial/marketing visuals, expresses AI through the **AI & Agents illustration pillar** (sine-wave geometry, pearl textures, and the AI-only pearl Mona variant where mascots are permitted).

*Cross-reference*: Inside Product UI (`@primer/react` / `@primer/octicons-react`), functional icons like `CopilotIcon` (and `Octicon` product triggers inside menus, avatars, or agent delegation rows) remain standard UI controls, distinct from marketing brand logos.

Sources: `https://brand.github.com/brand-identity/copilot/`, `https://brand.github.com/foundations/logo/`, `https://brand.github.com/graphic-elements/illustration/`.

## Co-branding and partner rules

Co-branding arrangements are evaluated case-by-case through GitHub Brand Studio, with strict defaults for third-party events, integrations, and sponsorships:

- **Visual parity**: Lockups must size the partner logo and GitHub mark to roughly equal optical area (accounting for both width and height) and center-align them optically.
- **Social announcements**: Sponsors or official speakers may include an approved GitHub mark in static graphics announcing participation.
- **Implied partnership ban**: Partners cannot claim `"Hosted with GitHub"` or `"Partnering with GitHub"` without explicit written permission.
- **Forbidden titles and mascots**: Never combine trademarked GitHub product names or mascots into third-party activation titles (explicitly barred example: *"Copilot and Cocktails"*).
- **Merchandise and partnership pages**: Third-party partner landing pages may include a secondary approved Invertocat/GitHub logo only with prior approval and never as the page's primary brand mark. Putting GitHub logos on partner swag, booth giveaways, or t-shirts requires explicit approval.

Sources: `https://brand.github.com/brand-identity/cobranding/`.

## Trademark and copyright guardrails

GitHub's legal guidance distinguishes legitimate ecosystem references from infringing imitation:

| Action | Status | Conditions |
| --- | --- | --- |
| Link to a GitHub repo or profile (social button) | Permitted | Use an unmodified permitted logo purely as a link to GitHub |
| Indicate third-party GitHub integration | Permitted | Keep GitHub logo visibly secondary/smaller than your own brand mark |
| Reference GitHub in journalism / blog posts | Permitted | Standard editorial reference without implying endorsement |
| Use GitHub marks as *your* app, company, domain, or social avatar | **Prohibited** | Violates trademark and falsely implies official GitHub authorship |
| Recolor, add effects, distort, or fuse marks with your name/icons | **Prohibited** | Zero modifications allowed |
| Reproduce mascots/illustrations/Brand Guidelines PDF artwork without permission | **Prohibited** | Octocat/Mona designs are federally registered copyrights; brand toolkit downloads carry *"All rights reserved"* |

Sources: `https://brand.github.com/foundations/logo/`, `https://brand.github.com/graphic-elements/mascots/`.

## Agent application notes

- **When building for non-GitHub products**: Adopt the *structure* of GitHub's brand thinking (3–5 concrete brand attributes spanning copy/visual/motion, clear separation between workspace chrome and campaign storytelling, disciplined copy ceilings), **not** GitHub's marks, `#0FBF3E` green, or mascot lore.
- **When building official GitHub marketing materials**: Enforce `GitHub` casing, select `Invertocat + Wordmark` for external/partner contexts, reject all deprecated Copilot mascot heads/hero logos, and never invent custom mascot poses (`Brand Asset Generator` or approved Brand Studio files only).
- **When reviewing copy**: Treat placeholder text (`Lorem ipsum`, `"TBD"`, generic AI slogans like `"Revolutionize your workflow today"`) as a blocking defect under `Authentic` and `Nerdy` precision rules.

## Gaps, caveats and source issues

- **Staff-only / inaccessible docs**: The detailed *Voice & tone guide* linked from `brand.github.com/brand-identity/voice-and-tone/`, Brand Guidelines Figma files, *Brand Asset Generator* web app, and Presentation PowerPoint templates are restricted to GitHub staff or approved agency partners; public agents cannot extract internal copywriting matrices or download proprietary Figma/vector packs.
- **PDF outline**: `GitHub-Brand-Guidelines-2026.pdf` (89 pages) uses outlined vector glyphs on most spreads, making full text extraction impossible without visual page inspection; this guide captures all readable HTML documentation from `brand.github.com` plus `@primer/react-brand` source code.
- **Public Toolkit minimum clearspace/size measurements**: Exact geometric clearspace multipliers (`x`-height grids) for logo lockups reside inside the downloadable vector asset kit / non-searchable PDF plates rather than HTML text on `foundations/logo/`. Agents needing exact print margins must inspect approved partner files rather than guessing ratios.
