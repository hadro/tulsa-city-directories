# Handoff: Tulsa 1921 City Directory Explorer — Design Polish

## Overview

This is a design-polish pass on an existing working data-explorer tool for the *Polk-Hoffhine Tulsa City Directory, 1921*. The underlying functionality already exists: a sortable / filterable / searchable table of 69,989 directory entries, a global search, sidebar facet filters, an entry detail viewer with a snippet from the source page scan, and CSV export.

The task is to **apply the visual language, layout decisions, and accessibility patterns documented here to the existing tool**, not to rebuild the tool from scratch.

The deliberate design decisions in this pass are:

1. **Editorial / archival tone.** The subject — a directory published months before the Tulsa Race Massacre, containing racial designations under Jim Crow — calls for restraint, gravity, and explicit contextualization rather than a "dashboard" aesthetic.
2. **Entry viewer moved from a right-side panel to a bottom drawer.** The page-snippet image is a wide-and-short crop; horizontal real estate suits it far better than the right rail.
3. **Field-coverage chart removed from the sidebar.** Per the designer's call, it was clutter.
4. **About modal + empty state.** Both are first-class surfaces: the About modal explicitly addresses the historical context and the "(c)" race designation; the empty state summarizes active filters and offers a one-click clear-all.

## About the Design Files

`Tulsa Directory Explorer.html` in this bundle is a **design reference** — a working HTML prototype showing intended look, layout, and behavior with mock data. It is not production code to copy directly.

Your task: recreate the visual treatment, layout, drawer placement, About modal, and empty state in the existing Tulsa Directory Explorer codebase using whatever framework/templating it already uses. Keep the existing data wiring, server interactions, and 69,989-row scaling work in place.

## Fidelity

**High-fidelity.** Exact hex values, type scale, spacing, hover/focus states, and accessibility behaviors are all documented below and present in the reference HTML. Recreate pixel-perfectly within the existing codebase's conventions.

---

## Design Tokens

### Colors

| Token | Hex | Use |
|---|---|---|
| `--paper` | `#f3ede2` | Page background, masthead, sidebar |
| `--paper-2` | `#ebe4d6` | Table-header background, range tag bg, kbd bg |
| `--paper-3` | `#e3dbc9` | Filter clear-X hover |
| `--card` | `#faf6ec` | Table body, drawer body, modal interior, inputs |
| `--rule` | `#d8cfba` | Default 1px rules between sections |
| `--rule-2` | `#c6bca3` | Stronger rule on inputs, buttons |
| `--ink` | `#1e1a14` | Primary text |
| `--ink-2` | `#4a443a` | Secondary text |
| `--ink-3` | `#5e574c` | Tertiary text / labels (AA-passing on paper) |
| `--ink-4` | `#80796c` | Quaternary muted text (large/non-essential only) |
| `--accent` | `#7a2a1f` | Single oxblood accent — sparingly: top bar of About modal, race-mark chip, selected-row indicator, primary action hover, links |
| `--accent-soft` | `#f0d6cb` | Race chip border, active-filter chip bg basis |
| `--row-hover` | `#ece5d4` | Table row hover |
| `--row-selected` | `#e6dcc4` | Table row selected |

All contrast ratios above 4.5:1 against `--paper` for the ink-1/2/3 family. `--ink-4` is reserved for non-essential and large text only.

### Typography

Three families, loaded from Google Fonts:

- **Spectral** (serif): 300, 400, 500, 600, italic 400
- **IBM Plex Sans**: 400, 500, 600
- **IBM Plex Mono**: 400, 500

```
--serif: "Spectral", "Source Serif Pro", Georgia, serif;
--sans:  "IBM Plex Sans", system-ui, sans-serif;
--mono:  "IBM Plex Mono", ui-monospace, Menlo, monospace;
```

**Type scale and assignments**:

| Element | Family | Size | Weight | Notes |
|---|---|---|---|---|
| Masthead title | Serif | 30px | 400 | Italic emphasis on directory name; `letter-spacing: -0.005em` |
| Masthead eyebrow | Mono | 10.5px | 500 | Uppercase, `letter-spacing: 0.14em`; small oxblood dot prefix |
| Masthead context line | Sans | 13px | 400 | `line-height: 1.55`, `max-width: 720px` |
| Masthead count | Serif | 22px | 400 | Right-aligned, paired with mono meta below |
| Page body default | Sans | 13.5px | 400 | |
| Table column headers | Mono | 10.5px | 500 | Uppercase, `letter-spacing: 0.12em` |
| Table row name cell | Serif | 15px | 400 | |
| Table other cells | Sans | 13.5px | 400 | |
| Range tag | Mono | 11px | 400 | `letter-spacing: 0.06em` |
| Race chip "(c)" | Mono | 10.5px | 400 | Oxblood text, accent-soft border |
| Sidebar section heading | Mono | 10.5px | 500 | Uppercase, `letter-spacing: 0.12em` |
| Sidebar facet row | Sans | 13px | 400 | Count in Mono 11.5px |
| Sidebar archival note | Serif italic | 12px | 400 | |
| Search input | Sans | 13.5px | 400 | 40px tall, 14px padding |
| Button | Sans | 13px | 400 | 40px tall |
| Drawer eyebrow | Mono | 10.5px | 500 | Uppercase |
| Drawer entry title | Serif | 24px | 400 | |
| Drawer metadata key | Mono | 10.5px | 500 | Uppercase, `letter-spacing: 0.12em` |
| Drawer metadata value | Serif | 17px | 400 | |
| Drawer metadata value (mono variant) | Mono | 14px | 400 | For ranges and Yes/No flags |
| About modal title | Serif | 40px | 300 | Italic emphasis in accent color on directory name; `letter-spacing: -0.015em`; `max-width: 22ch` |
| About modal section head | Mono | 11px | 500 | Uppercase, `letter-spacing: 0.16em` |
| About modal lede | Serif | 18px | 400 | |
| About modal prose | Serif | 16px | 400 | `line-height: 1.65`, `max-width: 60ch` |
| About modal numbered list counter | Mono | 11px | 400 | `decimal-leading-zero`, accent color |
| About modal side stat | Serif | 36px | 300 | `letter-spacing: -0.02em` |
| About modal side stat text variant | Serif | 22px | 400 | For labels like "1921 edition" |
| About modal side label | Mono | 10px | 500 | Uppercase, `letter-spacing: 0.16em` |
| About modal side sub | Sans | 12.5px | 400 | `line-height: 1.5` |
| Empty state title | Serif | 22px | 400 | |
| Empty state filter chip | Mono | 11px | 400 | Oxblood on accent-soft bg |

### Spacing

The design uses no formal scale token system — measurements are direct in pixels. Common values: `padding 14px` for table cells, `36px` left/right gutters for masthead and main column, `22px` for sidebar gutter, `56px 32px` for empty state, `26px 56px 56px` for About modal body, `48px` padding on About backdrop.

### Borders & radii

- All borders: `1px solid var(--rule)` or `--rule-2`
- Border radius: `2px` everywhere (intentionally minimal — archival feel)
- About modal has a 4px oxblood top stripe via `::before`

### Focus

Global `:focus-visible` ring:
```css
outline: 2px solid var(--accent);
outline-offset: 2px;
border-radius: 2px;
```

### Motion

- Drawer slide: `transform .28s cubic-bezier(.32,.72,0,1)`
- Drawer backdrop: `opacity .22s ease`
- Tooltip: `opacity/transform .15s ease`
- All wrapped in `@media (prefers-reduced-motion: reduce) { transition: none !important; }`

---

## Screens / Views

### 1. Main view

**Layout**: Fixed 256px sidebar on the left, fluid main column on the right. Above both, a full-width masthead.

**Masthead**:
- Two-column grid: title block left, meta block right-aligned
- Eyebrow ("PIPELINE DATA EXPLORER" in mono, preceded by a 5px oxblood dot)
- Title: "Polk-Hoffhine Directory Co.'s *Tulsa City Directory,* 1921" — "Tulsa City Directory," is italic in `--ink-2` color
- Context line below the title, max-width 720px: "Compiled and published in the spring of 1921 — months before the Tulsa Race Massacre destroyed the Greenwood district that May." followed by a quieter line: "Names marked '(c)' were designated as Colored in the original publication."
- Right meta: large serif count "69,989", "entries · 1921 edition", "one of 11 directories indexed", and a small oxblood dotted-underline link "About this directory →"

**Sidebar**:
- `position: sticky; top: 0; max-height: 100vh; overflow-y: auto`
- Filter sections in this order: Business · Race designation · Is rear · Special contract · Is advertisement
- Each section: mono uppercase heading with a "clear" button (sans, lowercase) at the right when any facet is active
- Facet rows: 13px label with custom 13×13 checkbox (filled with `--ink` when active, with a 6×6 paper dot inset), and a tabular-numeric count on the right
- Archival note at the bottom: serif italic, "Source scans courtesy of the Tulsa City–County Library. *About this directory →*"

**Toolbar** (above table):
- 4-column grid: search (1fr), Export CSV (primary), Random entry (secondary)
- Search input: 40px tall, prepended search icon, focus border darkens + 3px accent-tinted ring
- "Export CSV" is the primary action: inverted ink-on-paper, hover turns oxblood
- "Random entry" is secondary: card-bg with rule border
- Search hint below toolbar: 11.5px sans muted, contains the live "Showing X of Y entries · sorted by Z" status (`role="status" aria-live="polite"`)

**Table**:
- `border-collapse: separate; border-spacing: 0;` (REQUIRED — `collapse` breaks `position: sticky` on `<th>`)
- Sticky header: both the label row (`top: 0`) and the filter row (`top: 38px`) stick
- Column widths via colgroup: Range 92px, Name 22%, Business 90px, Spouse 14%, Race 80px, Occupation 18%, Address 24%
- Header cells contain a `<button class="col-sort">` for keyboard sort triggering; the `<th>` carries `aria-sort="ascending|descending|none"`
- Sort indicator glyph: ↕ (inactive), ↓ (asc), ↑ (desc)
- Filter row inputs: 28px tall, with a clear-X button that appears when the field has a value
- Rows are `tabindex="0" role="button" aria-label="Open entry: [Name]"`; focus ring is an inset 3px oxblood left bar + 1px ring
- Range column: pill tag with paper-2 bg and rule border
- Name column: serif 15px, with "(c)" rendered as a `<button class="race-mark">` chip that opens a tooltip on hover/focus
- Business column: "YES" as a paper-2 mono pill, "—" otherwise
- Spouse: italic ink-2 (the directory's own convention)
- Selected row: paper-3 background + 3px oxblood inset left bar via `box-shadow`

### 2. Bottom drawer (entry detail)

Triggered by clicking a row, pressing Enter on a focused row, or clicking "Random entry".

**Structure**:
- Backdrop: `rgba(30,26,20,0.18)` semi-transparent
- Drawer: full-width, anchored bottom, `max-height: 72vh`, slides up via `transform: translateY(102% → 0)`
- `role="dialog" aria-modal="true" aria-labelledby="drawerTitle"`
- Focus moves to close button on open (280ms after to let the slide complete)
- Tab is trapped inside; Esc closes; focus returns to the originating row

**Header**:
- Eyebrow: "Entry · Polk-Hoffhine 1921 · p. [N]"
- Title: serif 24px name, with "(c)" race chip appended if applicable
- Right side: close-X button (32×32, rule border)

**Body**:
- Snippet frame: 1px rule border, inner padding 14px, with a small mono caption "SOURCE SCAN · PAGE SNIPPET" top-right. Contains an `<img>` of the source-page region (in the prototype this is a stylized SVG placeholder with text-row patterns; production will use the real IIIF crop).
- Metadata grid: 3 columns × variable rows, 18px row gap, 36px column gap
  - Each field: mono uppercase key on top (10.5px) + serif value (17px) below; bottom 1px paper-2 rule
  - Empty values render in italic serif: "no value recorded"
  - Mono variant for keys with shortcode values like Range, Yes/No flags
- Footer field spans full grid: an oxblood "Open source page scan →" link with arrow icon

### 3. About modal

Triggered by:
- The "About this directory →" link in the masthead meta
- The "About this directory →" link in the sidebar archival note
- Deep link via `#about` URL hash

**Structure**:
- Backdrop: full-viewport `rgba(30,26,20,0.42)` + 2px blur, 48px padding
- Modal: max-width 1080px, max-height `calc(100vh - 96px)`, paper bg, scrolls internally
- 4px oxblood top stripe via `::before`
- Head: small mono eyebrow "About this directory" left, 32×32 close-X right
- Body padding: `26px 56px 56px`

**Title**:
- "An exploration tool for data from the *Polk-Hoffhine Tulsa City Directory,* 1921 — extracted from page scans."
- 40px serif, weight 300, with the directory name italicized in the oxblood accent color
- `max-width: 22ch` to control line breaks

**Body grid**: 2 columns, `1.5fr` left + `1fr` right (min 260px), 56px gap, `align-items: start`.

**Left column — prose**:
- Lede paragraph in serif 18px ink-1
- Five sections, each preceded by a small mono uppercase heading:
  1. *The source* — provenance, publisher, scan source
  2. *A note on the "(c)" designation* — rendered as the **emphasis blockquote** (left 2px oxblood border, italic, ink-1)
  3. *A note on accuracy* — OCR / extraction caveat
  4. *What's missing* — domestic workers, transients, etc.
  5. *How to use this site* — numbered list (see below)
- Numbered list: leading-zero counters in mono oxblood, hanging in the left margin; list items are serif 16px

**Right column — sidebar**:
- 1px rule left border, 32px padding, 28px row gap
- Four "side blocks":
  - Dataset: stat "69,989" + sub "individual listings, extracted from the 1921 edition"
  - Marked "(c)": stat "5,529" + sub
  - Volume: stat-text "1921 edition" + sub
  - Pipeline: stat-text "Directory Pipeline" + sub mentioning the open-source tool
- Credit at bottom: serif italic, 1px paper-2 top rule, "Source material held by..."

**Behaviors**:
- Esc closes
- Backdrop click closes (but click inside the modal does not)
- Focus moves to close button on open
- Focus trapped inside (Tab/Shift+Tab cycles within)
- Focus restored to the trigger element on close

### 4. Empty state (table)

Appears when filters yield zero rows. Replaces the table-wrap.

- Centered, padding `56px 32px 64px`, card background
- Title: serif 22px "No entries match the current filters."
- Sub: 13px ink-3 "Try removing a filter or broadening your search."
- Active-filter chip row (only if at least one filter is active): inline flex wrap of small oxblood-on-accent-soft mono pills, e.g., `name: "barnes"`, `race: (c)`, `search: "greenwood"`
- "Clear all filters" button: 1px oxblood border, accent-soft tinted bg, oxblood text, with a small × icon. Clears search, every column filter, and every facet. Sidebar re-renders.

---

## Interactions & Behavior

### Sorting
- Click a column header button or press Enter on it to sort by that column
- Clicking the same column toggles ascending → descending
- `aria-sort` updates on `<th>`

### Filtering
- Global search filters across all string fields (case-insensitive substring)
- Each text-filter column input shows a clear-X when non-empty
- Sidebar facets are click-to-toggle; counts shown next to each
- All filters compose AND-wise
- Active-filter summary appears in the empty state when results = 0

### Drawer
- Click row / Enter on focused row / "Random entry" button opens the drawer
- Click backdrop / Esc / close button closes
- Focus moves to close on open; restored to originating row on close
- Tab traps inside
- Prev/next navigation between entries: **intentionally removed** per the designer's decision

### About modal
- Triggers: masthead link, sidebar link, `#about` hash
- Click backdrop / Esc / close button closes
- Focus moves to close on open; restored to trigger on close
- Tab traps inside

### Keyboard
- `↑` / `↓` on a focused table row moves focus to adjacent row
- `Enter` / `Space` on a focused row opens the drawer
- `Esc` closes whichever overlay is open (drawer or about)
- All buttons, inputs, links reachable via Tab with visible focus ring

### Reduced motion
- `@media (prefers-reduced-motion: reduce)` disables drawer and backdrop transitions

---

## State Management

State that the existing tool already has:
- Sort column + direction
- Global search string
- Per-column filter strings
- Sidebar facet selections
- Selected entry index

New state introduced by this pass:
- Drawer open/closed (+ which entry)
- About modal open/closed
- Saved `document.activeElement` references for restoring focus on close (one for drawer, one for about)

URL state to consider adding (not implemented in the prototype):
- `#about` opens the About modal on load — already supported

---

## Accessibility

Implemented in the reference HTML, must be carried over:

- ✅ Color contrast ≥ 4.5:1 for all primary/secondary text against the paper background
- ✅ Visible `:focus-visible` ring globally
- ✅ Table rows focusable (`tabindex="0"`, `role="button"`, `aria-label`)
- ✅ Sort headers as real `<button>` elements with `aria-sort` on the `<th>`
- ✅ "(c)" race chip is a `<button>` with descriptive `aria-label`; tooltip opens on `:hover` AND `:focus-within`
- ✅ Drawer is a `role="dialog" aria-modal="true" aria-labelledby` with focus management and focus trap
- ✅ About modal same as drawer
- ✅ Count line is `role="status" aria-live="polite"` so filter results are announced
- ✅ Snippet `<img>` has a descriptive `aria-label`
- ✅ Filter inputs and clear-X buttons all carry `aria-label`
- ✅ Reduced-motion media query disables drawer + backdrop transitions

Items the prototype does NOT yet address — production should consider:
- Skip-to-main-content link
- Sidebar collapse for keyboard users when narrow
- Screen-reader announcement of drawer open/close beyond the dialog role
- Mobile/touch-target sizing for buttons (currently 40px; some chips and clear-X are smaller)

---

## Assets

- **Google Fonts**: Spectral (300/400/500/600 + italic 400), IBM Plex Sans (400/500/600), IBM Plex Mono (400/500). The reference HTML loads them via the standard Google Fonts CSS link.
- **Icons**: All inline SVGs in the reference HTML — search, download, dice, close, arrow, chevron, link-out, GitHub mark in the about modal. Currently hand-drawn 24×24 viewBox with `stroke-width: 1.75`, `stroke-linecap: round`, `stroke-linejoin: round`. Reproduce in whatever icon system the codebase uses (Lucide, Heroicons, or inline) maintaining the same stroke style.
- **Source-page snippet image**: stylized SVG placeholder in the prototype. Production already has real IIIF-cropped images — keep those, sized into the drawer's snippet frame.

---

## Files

| File | Role |
|---|---|
| `Tulsa Directory Explorer.html` | The full design reference — open in a browser to see every state. Mock data, no real backend. |
| `README.md` | This document. |

---

## Implementation order suggestion

If staging the rollout:

1. **Type + color tokens.** Get the paper palette, ink scale, and Spectral/Plex pair landed in the existing tool's tokens / theme file first. Everything else cascades from this.
2. **Masthead + sidebar polish.** Lowest-risk visual changes; no behavioral shifts.
3. **Table treatment.** Sticky headers (mind `border-collapse: separate`), filter clear-X, sort buttons, focusable rows.
4. **Drawer migration.** This is the biggest structural shift — moving the entry detail from a right-side panel to a bottom drawer. Plan accordingly if the existing right-side panel has features beyond what's shown here.
5. **About modal + empty state.** Standalone additions; no dependency on the table refactor.
6. **Accessibility audit.** Run a screen-reader pass (NVDA / VoiceOver) and a keyboard-only pass once the above are in.
