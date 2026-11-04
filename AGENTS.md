# Northside Voice: Playground and Console

Clickable prototype for the "Appointment Reminder Voice Agent" design challenge. Northside Health, a fictional clinic group, runs an AI voice agent that calls patients 24 to 48 hours before an appointment to confirm, and reschedules on the call. Playground is where an engineer builds and tests the agent. Console is where front-desk staff monitor it.

Plain HTML, CSS and JavaScript. No build step, no dependencies. Open `p01-templates.html` in a browser (`index.html` redirects there). All paths are relative, so the folder works from `file://` or any static host.

## Personas
- **Sam, 31, integration engineer.** Uses Playground. Reads JSON fluently, cares about latency and failing scenarios.
- **Teresa, 47, front-desk operations lead.** Uses Console. Not technical. Needs to know which patients need a human, fast.
- **Maria, 59, patient.** Never sees a screen. Every decision on these screens decides whether she shows up at the right time.

## File map
| File | What it is |
|---|---|
| `p01-templates.html` | Choose a template. Card grid with an always-visible details panel. |
| `p02-configure.html` | Edit instructions, models, functions, variables, guardrails (5 tabs). |
| `p03-live-call.html` | Live test call: transcript, call state, latency, guardrails. |
| `p03-alt-wrong-person.html` | Test call where a guardrail held (wrong person answered). |
| `p04-scenarios.html` | Test scenarios and results. One scenario fails by design. |
| `c01-deploy.html` | Deploy rules. Deploy is gated by an acknowledgement checkbox. |
| `c02-overview.html` | Console overview: Needs Attention queue, metrics, trend chart. |
| `c03-call-log.html` | Filterable call log. |
| `c04-call-detail.html` | A successful reschedule, end to end. |
| `c04-alt-needs-review.html` | The same failure reaching a patient. Resolve flow. |
| `notes.html` | Design notes: type scale, mono rule, reading order, color logic. |
| `northside.css` | All tokens and shared components. |
| `shell.js` | Left nav, product switcher, fidelity and annotation toggles, toast, demo state, tab helper. |
| `icons.js` | SF Symbols as inline SVG strings, `window.NSI`. |
| `fonts/` | Inter and Roboto Mono, bundled. |
| `assets/user-flow.svg` | The user flow diagram shown on the notes page. |
| `_source/` | Brief annotations and element checklists extracted from the design file. Not needed to run the prototype. |

Page-specific CSS and JS live inline in each page. Shared components belong in `northside.css`.

## The flow
P01 pick a template, P02 configure, P03 test call, P04 scenarios (scenario 3 fails), C01 deploy (must acknowledge the failure), C02 overview, C03 call log, C04-alt the same failure reaching Maria. Resolving it in C04-alt removes it from the queue and drops the counts from 6 to 5.

## Design rules
**Components come from the Wireframe Kit.** Buttons, badges, chips, fields, toggles, tabs, nav items, metric cards, bubbles, function blocks, variable chips, the waveform and annotation markers use the kit's radii, borders and fills (see the top of `northside.css`). Anything the kit lacks (table, banner, option card, checkbox, segmented control, disclosure, toast, chart) is derived from the kit's own borders and radii. Do not introduce new radii or a new accent color.

**Layout follows an existing admin-UI pattern:** left nav, page header with a hairline divider, main column plus a sticky right rail, slide-out drawers for details.

**Status is encoded by form, never by color alone.** Filled = done or passed. Outline = changed. Dashed = absent or not run. Muted fill = passive. Heavy outline with a "!" = needs a person. Color only confirms the form. `[data-fi="low"]` (an attribute `shell.js` sets on `<html>`) maps every color token back to neutrals, so each screen must read the same in grayscale. Test every change with `?fi=low`.

**Typography.** Inter for language, Roboto Mono for machine strings. Seven text styles plus mono: Display 40/44, Title 28/32, Headline 20/24, Heading 16/24, Body 14/20, Caption 12/16, Eyebrow 12/16 caps, Mono 12/16. Use mono only for text a machine produced that a developer might copy or compare: function names, args and results, status codes, latencies, IDs, `{variables}`, transcript timestamps. Never for anything a person said or wrote, and never for buttons or labels.

**Choosing between chips, tabs and segmented controls.** Chips filter one list or grid in place (P01 categories, C03 call outcomes). Underline tabs switch between sections that show different content (P02 sections, C04 transcript / function calls / audit log). The segmented control picks one value of a setting that rescopes a page (C02 date range). Do not mix them for the same job.

**Spacing and sizes** use even pixels only (4s preferred, 2s allowed). No fractional rem.

## Copy rules
- No em dashes in UI copy. Use commas, periods or restructure. An em dash as an empty-value placeholder in a table cell is fine.
- Title Case for buttons, titles, drawer headers, headings and tab labels. Articles (a, an, the) stay lowercase. Sentence case for prose, hints and placeholders.
- The middle dot (`·`) is for compact metadata only (`Oct 13, 2026 · 10:04 AM`). Never join clauses with it and never use it inside menu or list rows.
- Tone: reassuring, not alarming, even when something has gone wrong. Plain words for people, exact values for machines.

## Accessibility (WCAG 2.2 AA is a hard requirement)
- Contrast is computed from the tokens and listed on `notes.html`. Secondary text is `#626262`, not the kit's `#6B6B6B`, because the kit gray is 4.47:1 on its own muted fill.
- Edges of interactive controls use `--control` `#8C8C8C` (3.4:1). The kit's `#D1D1D1` (1.5:1) is kept for card hairlines only.
- Real elements: buttons are `<button>`, links are `<a>`, tabs use `role="tablist"`, switches use `role="switch"` with `aria-checked`, option groups use `role="radiogroup"`. No clickable `<div>`s.
- One `<h1>` per page, `<h2>` for sections, tables have a visually hidden `<caption>`.
- Focus is a single solid 2px ring (`:focus-visible`). Honor `prefers-reduced-motion`.
- Drawers and dialogs: put them outside any element you mark `inert` (a drawer inside an inert container is dead), return focus to the opener on close, close on Escape, the X and a click outside.
- Known gap: contrast was checked by calculation only. A screen reader pass and an automated audit have not been run.

## Shared JavaScript (`shell.js`)
Exposed as `window.NS`.
- `NS.ico(name, cls)`, `NS.chev()`, `NS.chevd()`, `NS.check`, `NS.play`: icon strings.
- `NS.wave(n, played)`: waveform bars in kit proportions.
- `NS.toast(msg)`: top-center status message (`role="status"`).
- `NS.tabs(listEl, onChange)`: roving-tabindex tab behavior with arrow keys.
- `NS.bindToggles(root)`: toggles marked `data-toggle`.
- `NS.get(key, default)` / `NS.set(key, value)`: localStorage under the `ns:` prefix.
- `NS.reviewCount()`, `NS.isResolved(id)`, `NS.resolve(id)`: demo queue state. The seeded count is 6 and the resolvable call is `call_3c71d0`.
- `NS.reset()`: clears resolved calls, `deployed`, `ack` and saved resolutions. The nav's Reset Demo link calls it.
- Query params: `?fi=low|high` sets fidelity, `?ann=on|off` shows or hides the brief annotations.

Each page declares `<body data-product="play|cons|ref" data-nav="p01">` and an empty `<aside class="side" id="side">`. `shell.js` fills the nav.

## Content notes
Taken from the brief and design file: personas, screen purposes, the appointment-reminders template (5 functions, 6 variables, 7 scenarios, 4 guardrails), the failure story, and all annotation text.

Invented: the wording of the P02 prompt rules, the other five templates, extra Call Log rows, the Today and 30 Days figures on C02, the suggested prompt fix on P04, and the C01 retry count (2, from the user flow's "max 2 per attempt"). Replace these with real values when they exist.

## Open items
- Screen reader and automated accessibility audit.
- Let a prompt fix in P02 flow into a passing re-run on P04.
- Guardrail weighting (P02 open question) is answered with a note, not a visual hierarchy.
- Compact density for the Call Log. Dark mode.
