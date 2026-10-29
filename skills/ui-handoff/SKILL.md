---
name: ui-handoff
description: Use when the user asks to create, update, or review UI handoff / dev handoff / spec boards in Figma (contents, scope with new/changed/reused spotlights, screen spec, variant × state matrix, component states, user flow, responsive breakpoints, edge cases, field validation, error and message catalog, status lifecycle, changelog) for frontend, mobile, or QA. Also use when the user wants CTA enable/disable rules, form validation rules, error messages, toasts, search / filter / sort rules, or entity statuses documented for developers. Works in any project: it adapts to the product's platform (responsive web, desktop app, mobile app, tablet), breakpoints, note language, fonts, and brand colours. Builds style-E boards that document behavior, rules, and conditions only, never what Figma Dev Mode already shows.
metadata:
  version: "5.3"
---

# UI Handoff — Style E (Figma)

Handoff boards built **in Figma**, next to the designs, that explain **what Dev Mode can't**: conditions, behavior, state logic, flow, edge cases, and the **business rules** behind them (when a button is disabled, what a field accepts, which error shows, what a status means). Every instruction below is in Figma terms: frames, auto layout, components, instances.

> **Codes are internal.** Page numbers (01–10) and codes in this skill (`E#`, `FV`, `MSG`, `ST`, `SV`, `OP`, `B#`…) are Claude's shorthand for building and checking the handoff. **Never print them on the canvas.** The team reads names: board names, element names, field labels, message titles, status labels. The only numbers on a board are marker numbers (§1).

## How Claude should work

0. **Set up for this project first (§0a).** In a file or project you haven't worked in, build the project profile (platform, sizes, language, fonts, accent, kit, dev names) before anything else. Reuse it on later runs.
1. **Ask first, never guess.** Before building, confirm, in one message:
   - **the feature**: what it is, who uses it, where it starts and ends, and what's new or changed. Ask about the feature, never for a list of screens or boards: Claude works out the screens, components, and page types from the feature and the designs, then shows the Scope back to the user to confirm before building.
   - **where things live** (§0a, Locations): the handoff boards, the handoff kit, the design system, and the screen components. Four separate answers, never one "Figma link".
   - the source of behavior (PRD, ticket, API spec, user notes, existing designer notes on the canvas)
   - the **team's note language** (§1)

   Missing rules become `TBD` + Open questions, never invented.
2. **Collect the rule inventory before drawing.** Read the source and the existing designer notes, then sort every rule into the §1b buckets. Each bucket with at least one rule needs a home on a board. A bucket the feature clearly needs but the source is silent on becomes an Open question.
3. **Check masters.** If the source screens aren't components yet, say so and make them components (or ask the user to) before placing anything.
4. **Build one board at a time** in the §2d canvas order, following §2–§6.
5. **Run the §7 checklist** before reporting done, then list every `TBD` back to the user.
6. **Edit, don't rebuild.** For a change request, touch only the affected items and add a changelog row.

## 0a. Project setup — adapt to the project

This skill fixes the **handoff's** look and structure, not the product's. Everything that depends on the product comes from the project, never from an earlier project or from the examples in this file. Examples here (`Amount`, `Order history`, `Status`) are placeholders; use the project's real names.

Before the first board, fill in this profile. Look in the design file, the design system, and the source first; ask the user only for what you can't find, in one message.

| Setting | Where to find it | If it isn't given |
|---|---|---|
| **Platform** | The designs (frame sizes, device frames, OS components), the PRD | Ask. Never assume desktop. |
| **Size set** | The design system's breakpoints or grid styles, the frames the designers drew, the PRD | Use the default for the platform (below) and say so in the profile |
| **Note language** | The user, the existing designer notes | English |
| **Fonts** | Load the handoff fonts with `figma.loadFontAsync` | Fallback pair (§3) |
| **Accent** | The product's brand and UI colours in the design system | Violet, unless the product UI uses purple (§2d, Accent) |
| **Handoff kit** | The kit location (Locations, below): components named `Handoff / …` | Build the kit on a `Handoff kit` page (§4) |
| **Dev names** | API spec, backend ticket, the component library's naming | Leave them out; `TBD` + Open question where the dev needs one |
| **Rule sources** | PRD, tickets, API spec, designer notes on the canvas | Ask; missing rules become `TBD` |

**Locations — ask for each one separately.** A handoff touches four places, and teams keep them in different files. Never assume they're the same file, and never write into a file the user didn't name for that job.

| Location | What goes there | Ask | Default if the user has no preference |
|---|---|---|---|
| **Handoff boards** | The five lines of boards (§2d) | Which file and page should the handoff go on? | A `Handoff · {feature}` page in the feature's design file |
| **Handoff kit** | The `Handoff / …` components (§4) and the `Handoff profile` | Is there a handoff kit already (file or library)? Where should it live? | A `Handoff kit` page in the same file as the boards |
| **Design system** | The product's components, styles, and tokens: read only, never edited | Which library is the design system? | The libraries enabled in the file; if none, ask |
| **Screen components** | The masters the boards show as instances (§0) | Where are the screens? Where may Claude turn them into components? | A `Handoff sources` page next to the boards; the designers' own pages are left untouched unless the user says otherwise |

- Read the design system; never add to or change it. Handoff components never go into the product's design system library.
- If the screens live in a file Claude may not edit, place the masters on the `Handoff sources` page as components made from copies, and say so in the profile and the changelog, so the team knows where to update them.

**Default size sets by platform** (use the design system's own set whenever it has one):

| Platform | Sizes (name · viewport) | Sizes line |
|---|---|---|
| Responsive web, desktop first | XL · 1920 × 1080 · L · 1440 × 900 · M · 1280 × 800 · S · 1024 × 768 (+ Mobile · 390 × 844 if the site supports phones) | Yes |
| Responsive web, mobile first | Mobile · 390 × 844 · Tablet · 768 × 1024 · Desktop · 1440 × 900 | Yes |
| Native mobile app | The phone sizes the team supports, e.g. Small · 375 × 667 · Standard · 393 × 852 · Large · 430 × 932 (+ Tablet · 820 × 1180 if supported) | Yes, if the layout reflows by device |
| Fixed-size desktop app or one-size screen | The one size the designs use | **No.** Skip the Sizes line and the Breakpoints board; the Screen size cell says the one size, Other sizes `—` |

- Size names are the team's names when they have them (`Large · 1280`), otherwise the names above. On the canvas, always name + viewport, never `B2`.
- If iOS and Android designs differ, treat each OS as its own Sizes row for the screens that differ, and say which OS each Screen spec shows in its context strip.
- The board stays 1920 wide minimum on every platform; mobile screens simply leave room for more cards per row.

**Keep the profile with the file.** Put it on the `Handoff kit` page (Locations, above) as one small locked frame named `Handoff profile` (a table of the settings above), never on a board line. On later runs read it instead of asking again, and update it when the user changes a setting.

## 0. Before you build — masters first

1. Turn every source screen into a **component** (⌥⌘K). Turn every state into a **variant** of one component set (`state = S1 | S2 | …`).
2. Keep masters where the user said in Locations (§0a): the designers' page, or a `Handoff sources` page. Name each one plainly: `Order history — first page`.
3. Boards only ever hold **instances** of those masters, so design edits update the handoff automatically.
4. If a screen is still a plain frame, stop and make it a component before you place it on a board.

## 1. Content rules

- **Never invent behavior.** Only write rules, conditions, triggers, limits, messages, and results the user stated or that appear in the source (PRD, ticket, API spec, design, designer notes). Don't infer "smart" rules (hide-when-equal, truncation, fallbacks, defaults, sorting, permissions, validation ranges, error copy). If something is missing, put the **TBD tag** (§4) in its place and add the question to *Open questions*, with who to ask in Asked to.
- **Describe, don't extend.** For a plain element, name what it shows ("Account name · plan type · account ID") and stop. A note without a condition is fine.
- **Show it, then explain it.** Every variant, option, type, state, or status a note or table describes must also appear as a **preview of the real component in that variant**. A table of options with no previews is never enough: the dev has to see what the component looks like for each kind of option. Text explains the picture; it doesn't replace it.
- **Bullets, not prose.** Every behavior cell uses a Figma bulleted list (⌘⇧8): one fact, rule, or formula per bullet, no trailing periods. The element name is the bold title above the list.
- **One rule shape.** A condition is one line: `<condition> → <result>` (e.g. `Required field empty` → `Submit button disabled`). Put the condition first, because that's what a dev turns into an `if`. It's written beside the UI it changes (§1c), never on a separate board.
- **Write in the team's language.** Notes may be in the team's working language if the user asks for it. Quoted UI copy is always the **exact text** in the language it appears on screen. Board titles, pills, sub-headings, and table column headers stay in English so boards sort and search the same way.
- **Icon sets are documented, not just drawn.** When an icon changes with a value (a type, status, channel or result), add an icon table where that element is specified: one row per value — value (quoted) · the icon at real size · its icon name as a code chip (e.g. `icon-phone`) · notes. List every value the source has, even ones no master shows yet (say so in Notes).
  - **The icon comes from the real design.** The icon cell holds an instance of the component that draws it, set to that value (or a clipped crop of that instance showing just the icon and label), never a font glyph, a typed icon name or a redrawn shape. A dev selects it and lands on the real layer.
  - **Badge and tag sets get the same table.** When a badge or tag changes look with a value (tone, result, source, with / without a sub tag), add a table with one row per case: case · the real component instance in that variant · the component and variant as a code chip (`Badge · Tone=Warning`) · notes. Several-at-once cases (several tags in a row) are their own row.
- **Every value set gets its details.** Listing the values is not enough: for each set the dev has to implement (types, platforms or channels, results, statuses, tags), give its Option data table (§5, 03b) detail columns, one row per value, with what the source gives: the API value (code chip) · where it comes from (picked by the user, set by the system, or both) · who sees it (role) · when it's shown or hidden · what it means or maps to (e.g. per type: when the result is “Completed” vs “Failed”, and what an automatic record sets). Group rows by their parent value (platforms under their type). A detail the source doesn't give is `TBD` + an Open question, never a guess.
- **Every click has a next state.** If an element reveals or changes something in place (Show More / Show Less, expand, a menu), its result gets its own card right after the state it starts from, with `Next →` linking them. No master for it → the default placeholder + an Open question to design.
- **Behavior over pixels.** Never write spacing, sizes, colors, radii, type specs, props, or tokens. Devs read those in Dev Mode. Value **formats** (currency, decimals, dates) are behavior, not styling; document them in §1b.
- **UI copy is quoted, never boxed.** When a note names something the user sees on screen (a label, button text, tab, option, message, status badge), write the exact copy in curly quotes, Google Sans SemiBold 600, ink, the same size as the text around it: “Other”, “Retry”, “Request access”. Put the element type in plain text after it (“Retry” button). No fill, no stroke, no frame: a white box with an outline looks like a real button sitting in the table, and a fixed box gets sliced at the cell edge. Quoted copy is a text run inside the sentence, so it wraps with it and can't be cut off.
- **Names, not codes.** On the canvas, refer to everything by the name the team already uses: the element (`Next` button), the field (Amount), the message title (`Discard changes?`), the status (`Needs review`), the breakpoint (Large · 1280), the card title (Empty). Never add a code in brackets after a name (`Needs review (ST1)` is wrong).
- **Cross-references use names.** Point to another board as `See {board name} › {item}`: `See Field & validation › Amount`, `See Messages › Discard changes?`. When two items share a name, add the difference in words (`Something went wrong — timeout`).
- **Dev names are welcome; our codes are not.** Real system names help the dev find things in code and Dev Mode, so show them whenever the source gives them, never invented (TBD tag otherwise, asked to backend):
  - **API / enum value** of a status or option (`ACTIVATED`, `PENDING_INACTIVE`), next to the label the user sees.
  - **Backend codes that map to a display status** (`Pending` ← `1, 7, 8, 10`) when several backend states show as one label.
  - **Figma component path** (`OrderHistory / Status`) on component boards, exactly as named in the library.
  - Style: the **code chip** (§4): Geist Mono SemiBold 14, violet `#6D28D9` on `#E4D7FD`, radius 4, padding 2 / 8, never wraps. One style for every code: API values, backend codes, `{code}` in messages, Figma component paths, `Type=Primary`. Never a second purple style.
- **Markers are plain numbers.** A marker on a preview shows `1`, `2`, `3`…, restarting on every board, and the matching row or bullet starts with the same number. That's the only numbering on a board: rules are never numbered, they're written out beside their element (§1c).
  - **A marker never covers the UI.** Place it just outside the element's top-left corner (overlapping the corner by 6px): on the button, field or pill if the element has its own shape, otherwise on the label's text box. Never on top of the label, never centred on a button. Every frame that holds a marked screen (screen frame, card preview, step column) has **Clip content off**, so a marker past the screen's edge is never cut. Flow nodes are the exception: their markers sit on the flow canvas, so the node keeps **Clip content on** (otherwise its square header corners poke out of the rounded node).
  - **One number, one row.** Each marker number appears on exactly one table row or bullet. If one area of the screen holds two elements, give it two markers (`2`, `3`) side by side, never one marker shared by two rows. Number markers in reading order (top → bottom, left → right) and renumber after any change, so there are no repeats and no gaps.
- **New is new; existing stays existing.** When a feature adds a tab, page or section next to one that already exists, name it as the new item and say the existing one is unchanged (“A new Activity tab, after Notes… The existing History tab is not changed”). Never describe the new item as a change to the existing one, and never reuse the existing one's name for it.
- **No sign-off, owner, or status blocks.** The version lives only on the changelog page. (Record statuses like `Pending` / `Expired` are product data and belong on 10, §5.)

## 1b. Rule layer — what devs ask about

Most handoff questions are about rules, not layout. Sort every rule from the source into one of these buckets, and put each bucket in its fixed place. Never scatter rules into free-floating notes.

**Conditions sit beside the UI they change; values stay in their tables.** Every `condition → result` from the action, display, screen-variant, option-set, limit and field-validation buckets is written in the **Rules** cell of its element's row on 01 Screen spec, beside the screen (§1c). A rule that changes what the user sees is also drawn as a state card on 06, with the same line under it. The other boards named below hold **values** (the option list with API values, the field defaults) without repeating the conditions. Messages, statuses, transitions and formats keep their own tables (09, 10), because those are reference data used across items.

| Bucket | What it covers | Where it goes | Internal ref (never shown) |
|---|---|---|---|
| **Action rules** | Enabled when / disabled when / hidden when for every CTA, link, and icon button; what happens on click (navigate, open modal, submit, confirm first) | 01, the button's row in the element table; the journey is drawn on 04 User flow | `E#` |
| **Display & eligibility** | Which records/items are shown or excluded, and who can see or use the screen (e.g. only items the user is allowed to act on) | 01 context strip **Condition** cell + the affected elements' rows | `E#` |
| **Screen variants** | Other versions of the whole screen and the condition that shows each: role, record status, plan, feature flag, data (not component variants, which are on 02 / 03b) | 01 rows of what differs; 06 cards show each version | `SV-0#` |
| **Option sets** | A component whose content changes with what the user picks (checkbox / radio groups, tag pickers, dropdowns that reveal sub options, badges, or extra fields) | 03b, picker + **Option data** table side by side; conditions in the component's 01 row | `OP#` |
| **List controls** | Search fields and match type (exact / prefix / fuzzy), filters (multi-select, `Select All` / `Clear`, date ranges), sort options + default + direction labels, pagination or scroll area, selected-count summary | 01 **List controls** block | `L#` |
| **Limits** | Max items, max length, quotas, and what happens when the limit is reached | 01 element notes, and the limit-reached case on 06 | `E#`, `EC-0#` |
| **Field validation** | Per input: required, min / max / range, allowed types, when it's checked, the inline message, effect on the submit CTA | 08 Field & validation | `FV-0#` |
| **Messages & errors** | Every toast, banner, confirm modal, error modal, and inline error: trigger, exact copy, error code, recovery action | 09 Messages & errors | `MSG-0#` |
| **Status lifecycle** | Each status of the main record (not the UI component): label shown, its API value, the backend codes that map to it, meaning, who or what sets it, allowed actions | 10 Status & formats | `ST#` |
| **Transitions & timing** | How the record moves between statuses: trigger, confirm step, feedback, when it takes effect (now / next day / at a time), whether a pending change can be cancelled, and time windows when an action is blocked (cut-offs) | 10 Status & formats, **Transitions table** | `ST#→ST#`, `TW#` |
| **Formats** | Currency symbol and decimals, unit decimals, date / time format, empty-value display | 10 Status & formats | `FMT#` |
| **Navigation type** | Same page, new tab, modal, sheet, back, external link | 04 arrow labels | `F#` |

- A rule lives in **one** place and is cited everywhere else (a state card carries `Rule 4`, or says `See Field & validation › Amount`; it doesn't repeat the rule).
- Only fill rows the source supports. An empty bucket the feature obviously needs (e.g. a form with no validation rules given) → one `TBD` row + Open question.

## 1c. Pictures first, rules beside them

Devs build from pictures, and they miss conditions that sit on a separate board or in prose far from the UI. So every rule is written **beside the thing it changes**, in words, and drawn when it changes what the user sees.

- **Screen spec holds the rules.** Every step of the item at 100% on the left; on the right, the element table: `#` · Element · Rules. Each row lists that element's rules as `condition → result` lines (defaults, who sees what, what picking or clicking does, when it's disabled), with exact UI copy quoted. Where a rule has a state card, the line ends with `See States › {state}`.
- **States show the rule as a picture, with the rule under it.** Each state or variant card on 06 is the whole screen or modal at 100% with an accent outline on what changes, and its note is the same `condition → result` line, in the same words as its Screen spec row. Never a bare reference (`Spec › 4`) as the only note: a dev reading the picture must not have to look elsewhere to know why it looks that way.
- **Never number rules.** No `Rule 3` tags, no rule IDs, no separate Rules board. Markers number elements; rules are words beside them.
- **Value tables hold values only.** Option data and Field & validation tables list labels, API values, defaults, who can pick, required: no column that only points back to Screen spec.
- **UI left, table right.** Wherever a table explains a picture (Screen spec elements, option sets, fields), the real UI sits on the left and the table on the right, and both use the same marker numbers.
- **Draw only what the product draws.** Check the source before drawing a hover message, tooltip or hint: a browser `title` attribute is not a designed component, so describe it in words (`on hover, the browser tooltip shows …`) instead of drawing a tooltip. Draw it only when the product uses a real component for it.
- **Word budget.** If a board's text is longer than its pictures, cut the text. A Details column should read in a few minutes.

## 2. Previews

- **Instance, never a copy.** No pasted, duplicated, detached, flattened, or exported screens.
- **Real size, always 100%.** Every preview is the master instance at 100%: never scaled down with the Scale tool (K) and never resized. A scaled screen reads as a different UI and its text is unreadable; a resized one reflows. The board grows to fit the screen instead (Hug, min 1920). Never rebuild a screen from shapes. After placing each preview, compare it with the source frame: every button, tab, and label must read in full. **The one exception is 05 Breakpoints**, which is also 100% but crops the height to the viewport (see below).
- **Show the proof.** Everything a note mentions (a Carried Forward row, a "(cont. 1/N)" heading, a footer page number, a disabled CTA, an inline error) must be visible and readable in the preview. Never crop it out.
  - The full screen at 100% is the preview; no thumbnails and no zoomed detail crops.
  - Add a marker on each referenced area, labelled with that bullet's number (see Markers, §1).
- **Overlays sit on their host — once.** Where a modal, sheet, toast or dropdown appears is shown on its host (host under a 45% ink scrim for modals and sheets, unscrimmed for toasts and dropdowns) on **Breakpoints** and in the flow's Overlay nodes. State, option, field and message cards show the overlay alone at 100%. Never add an "opens on" host thumbnail to every card: the same context repeated on every card is noise.
  - A modal shown on its host follows the product's height rule (e.g. max height, then the body scrolls with header and footer fixed). Never let the frame cut off its footer.
- **Test the longest content.** Before calling a layout done, mock the longest real value of every field it shows (name, type + platform, date, result, tags, summary) as its own state (`Longest content`) and check it at every breakpoint. Nothing may cut off, overlap, or leave a separator dangling at a line end; if it does, the layout changes, not the data.
- **Screens get their real size.** Any full-screen state (edge cases, empty, loading, error, flow detail) is the master at 100% — a 1280-wide page is 1280 wide on every board. Never thumbnail-sized cards.
- **Several points on one screen → markers, not crops.** The screen is already at 100%, so every point a note makes gets a marker on the screen and a bullet with the same number. Never add zoomed detail crops or a detail strip.
  - More than 6 markers on one card → the screen needs its own 01 Screen spec board.
- **The table fits the component, not the other way around.** In matrix and state tables, each instance column is the component's real width + cell padding, using Fixed width columns. Never Hug the instance down, crop it, or set "Fill container" on it, and never tidy or re-flow items to fit a column.
  - Total wider than the board → the **board grows**: board width = Hug, min 1920. Don't shrink columns.
  - Wider than about 3,800px → split the states across two boards (S1–S3, S4–S6) with the same variant rows, or turn the table so states are rows.
  - All instances in a table use the same scale (100% by default) so states compare 1:1.
- **Horizontal cards — the default for every multi-item page** (03 states, 03b types, 05 breakpoints, 06 edge cases, 08 field states). One card per item, **all in one row, left → right, never wrapped**.
  - **Separate cards, not a shared grid.** Each card is its own frame (component `Handoff / Card`): **no fill and no outer stroke**, so it sits straight on the white board; radius 12, Clip content on. Gap 24 between cards, align top. Cards hug their content.
  - **Card, top to bottom:**
    - **Header row:** full card width, auto layout horizontal, space between, align center, padding right 24.
      - **Left · tab** (`Handoff / Dark header cell`, card variant): fill ink `#141414`, white text, padding 12 / 24, top corners radius 12, width Hug. **Title only** (Google Sans Medium 18).
      - **Right · meta**, outside the tab, on the white board, gap 6: meta text (Geist Mono 14, `#6B6B6B`: `Next → {state names}`, the range, or what it links to) → optional **marker ref** (Geist Mono SemiBold 15): the number of the marker this card belongs to. No meta → leave the right side empty.
      - **Scope tag goes in the header, never in the notes.** When a card's own item is new, changed, or reused (a component type, a variant, a state, a part of a screen), its Scope tag (§4.12) sits first in the right side of the header row, before the meta, gap 10. Never add a `Scope` section to the notes panel: the tag describes the whole card, so it belongs with the card's name. Cards whose board already carries the same tag in its board header (every card shares it) don't repeat it.
    - **Header rule:** a **2px** ink `#141414` line across the full card width, directly under the header row, so the tab sits on it. Never drop it; it's what ties the tab, meta, and preview into one card.
    - **Preview:** below the rule, no fill, padding 24 / 0, auto layout **centered horizontally**, so an instance narrower than the card sits in the middle. Screens and placeholders have **no border**: the screen is the UI exactly as designed, with no outline added around it.
    - **Notes panel:** fill `#E3E3E3`, no stroke, bottom corners radius 12, padding 24 / 24 / 28, gap 20. Each section: label (Geist Mono 14, `#6B6B6B`) → 8 → bullets (Google Sans 16 / 145%, `#2B2A31`, bullet + 10 gap). On state, variant and option cards the panel holds the card's rule as one `condition → result` line (§1c), plus at most one short line per marker. Breakpoints keep their Layout change / Hidden sections.
    - **Information sits on grey, UI on white.** Notes panels (`#E3E3E3`), context strips and table cells (`#F1F1F4`) are information surfaces; screens are the UI as designed, with no border. Tags and code chips always set their own text color; UI copy is quoted text, not a chip (§1).
    - No detail crops: the screen is already at 100%.
  - **Order cards by flow, not by when they were made.** Read left → right, top → bottom like the user meets them.
    - States: interaction first (Default → Hover → Focus → Pressed), then system (Loading → Success), then blocked (Disabled) last.
    - Screen variants: Default first, then by how many users see them, most → least. Each card states only what differs from Default, naming the elements.
    - Edge cases: data lifecycle first (Loading → Empty → No results → Error), then environment and access (Offline → Maintenance → No permission), then data changed between steps (stale / conflict, e.g. the record changed or was taken by someone else), then limits (limit reached), then time windows (cut-off, outside hours), then content (Long content).
    - Option variants: the most common kind first, then the other kinds in the order the options appear in the UI, the kind that needs input (`Other` + note) last, then combined cases.
    - Types: most used → least used (Primary → Secondary → Ghost → Destructive → Icon only).
    - Field states (08): Empty → Focused / typing → Dropdown open → No match → Valid → each invalid rule in `FV` order.
    - After reordering, update every `Next →`, `States S…`, and `See FV/MSG` reference.
  - **Text never cuts off.** Every text layer is auto layout **Hug height** (never Fixed), width Fill container, Clip content off on text frames. Card Clip content only clips images. Long words wrap; no truncation, no ellipsis, no fixed-height note boxes. Only tags, code chips, and markers stay on one line.
    - **In Figma terms:** every text layer in a note, cell, or strip is `textAutoResize = HEIGHT` with `layoutSizingHorizontal = FILL`. Never `WIDTH_AND_HEIGHT` there (it grows sideways past the cell edge), never `NONE` or `TRUNCATE`, and never `textTruncation = ENDING`. Only single-line labels (tags, chips, tabs, button text in a component) are `WIDTH_AND_HEIGHT`, inside a parent that Hugs them.
  - **Header alignment:** tab on the left with the title only; meta and marker ref on the right of the same row, vertically centered. Never put the meta inside the tab.
  - **One row, the board grows.** Cards never wrap to a second line: a dev reads the states left → right like the screens in a flow. The board is Hug width (min 1920) and gets as wide as the row needs. Never shrink or stretch a card to fit.
  - **Only the card row grows.** When a board gets wider than 1920, its header, context strip, rule tables, and Open questions keep a **fixed width of 1680** (the 1920 board minus padding), left-aligned. Set them to Fixed width, never Fill container, so table columns don't stretch across a wide board.
  - More than 8 cards → split into two boards stacked in the same column (e.g. `Edge cases · 1 of 2`), cut at a group boundary in the card order, never mid-group.
  - Card width = the screen's or component's real width + 48, on every board (a 1280 page → 1328 card, a 520 modal → 568). The board grows; never shrink a card or its preview.
  - **05 Breakpoints shows real pixels, never scaled down.** Each preview frame is the breakpoint's actual size at 100% (the project's size set, §0a; e.g. for desktop-first web 1920 × 1080, 1440 × 900, 1280 × 800, 1024 × 768), holding the master instance at 100%. The size badge Figma shows on the frame must equal the breakpoint (a 1280 card whose frame measures 960 × 768 is wrong).
    - Card width = breakpoint width + 48 padding. One row per section, largest → smallest; the board grows (Hug) to fit (four desktop sizes come to about 6,200 wide).
    - Don't use the Scale tool (K) here (or anywhere), and don't shrink frames to fit the board. A dev measures and compares against these frames, so they must be 1:1.
    - **Fixed height = the viewport, never the full page.** Breakpoints answers one question: what the user sees on a screen of that size, and how the layout reflows. The whole UI, top to bottom, is already on Screen spec. So every frame is Fixed height at that breakpoint's viewport height (not Hug), with Clip content on, and the instance is pinned to the top. Content below the fold is cut off on purpose.
    - Every card in a section therefore ends at a predictable height, and cards stay top-aligned and comparable. Never let one card grow to show more of the page than the others.
    - A frame never shows a scroll position other than the top, unless the note is about something that only appears after scrolling (a sticky header, a back-to-top button). Then use a second frame of the same fixed size, scrolled, labelled `scrolled`.
    - Need to point at something below the fold? Write it in the note and cite `See Screen spec`. Don't stretch the frame.
    - Every frame uses its size's real viewport height (e.g. 390 × 844 for a phone), and the card header states name + viewport (`Mobile · 390 × 844`).
  - Mark what changed with a 2px accent outline + numbered marker; start the matching bullet with the same number.
  - Tables are only for 02 Matrix, 07 Changelog, the element table and List controls on 01, 03b Option data, 08, 09, and 10.
  - **A table that explains a picture sits beside it.** UI on the left, table on the right, top-aligned, same marker numbers on both (01 elements, 03b option sets, 08 fields). A long table repeats its picture beside each group of rows, because nothing stays pinned while a dev scrolls the canvas.
- **Tables fit their frame; nothing is ever cut off.** Every table except 02 Matrix is exactly 1680 wide, and all its content stays inside.
  - Columns: Fixed width only for short values (a name, Yes / No, a time); the longest-text column is **Fill**. Every column must be wide enough for its header on one line; a header that wraps (`Shown` / `as`) means the column is too narrow.
  - Cells: Clip content **off**, text layers Hug height and Fill width (`textAutoResize = HEIGHT`, `layoutSizingHorizontal = FILL`), so long text wraps onto more lines instead of running past the edge. A cell's auto layout must itself be Fill width; a Hug-width cell lets its text push out sideways.
  - **Marker column.** The `#` column holding marker pills is Fixed width = pill width + both cell paddings (a 32px pill with 24 padding → at least 80, use 96), the pill centered, and Clip content **off** on that cell. A marker must always show as a full circle; a sliced pill means the column is too narrow.
  - **Several values in a cell wrap.** A cell listing several UI values (“Email”, “Phone”, “Chat”…) writes them as one quoted, comma-separated text run, which wraps like any text. A row of code chips uses horizontal auto layout with **Wrap** on, gap 6 / 6, width Fill, so the chips break onto the next line.
  - More columns than fit at a readable width (about 5) → split into two tables, or move long descriptions into the row as bullets. Never let the table run past 1680 or past the board edge.
  - **Check before handoff, by script, not by eye.** Walk every text node on the board and flag any whose box runs outside its parent frame's box, any with `textAutoResize` `NONE` / `TRUNCATE` or `textTruncation` `ENDING`, and any `WIDTH_AND_HEIGHT` text inside a table cell or notes panel. Fix every hit, then re-run until it's clean. Then drag the board's right edge in; if any text, chip, or column disappears instead of wrapping, fix it.
- **Fit, don't crop.** Preview frames keep the master's aspect ratio. If the preview is too small, widen the column. Don't shrink the component.

## 2d. Canvas layout — the handoff story

The canvas follows how the feature is presented: **big picture → what's affected → each piece up close → how it adapts → what changed.** It reads **top → bottom, one line per part of the talk**, every line starting at the same left edge, 240 apart.

| Line | Question it answers | What's on it |
|---|---|---|
| 1 · Overview | Where does this feature start, where does it end, and what's in this file? | **Contents** board → **User flow** (04) |
| 2 · Scope | What does this feature touch, and which rules apply everywhere? | **Scope** board · second row: **Shared rules** (shared Messages 09 → Status & formats 10) |
| 3 · Details | How does each piece work? | **One column per Scope item**, left → right in Scope order; boards stack top → bottom inside each column |
| 4 · Sizes | How does everything above adapt to each screen size? | **Breakpoints** (05): one row per screen and modal, same order as Scope, sizes left → right |
| 5 · Log | What changed since the last handoff? | **Changelog** (07), always last on the canvas |

Skip a board an item doesn't need; never reorder.

**Line 1 · Overview**
- **Contents** board (new page type): lists every line, and under Details every item with its position (`2 of 5`), kind (screen / component) and Scope tag. In Figma each name is a link to its section.
- **User flow** next to it. Flow nodes are named exactly like their Details columns, so the flow doubles as a map of the columns.

**Line 2 · Scope**
- **Scope** board (new page type), one card per item the feature touches (§5).
- Item order, used everywhere after this: **screens and overlays in the order the user meets them, then components.** REUSED items are listed last and get no Details column.
- **Second row · Shared rules:** messages used on more than one screen, and Status & formats. A message shown on only one screen goes in that screen's Details column instead.

**Line 3 · Details — columns**
- One Figma Section per item, side by side, left → right in Scope order, top-aligned, 120 apart.
- Inside a column, boards stack **top → bottom**, 120 apart, left-aligned:
  - **Screen or overlay:** Screen spec → Component types & variants (its option sets, if any) → Screen variants & edge cases → Field & validation → Messages (this screen only). Where each click goes is a line in its button's Screen spec row; the journey between screens is the User flow (Overview, or the column's own flow for a step with a sub-flow), never a second per-item flow board.
  - **Component:** Types & variants → Component states → Variant × state matrix.
- **No column header row.** Each Details board says where it belongs in its own **location pill** (`Details · 1 of 2 · Screen name`), with the item's Scope tag right next to it at the same size. Read across to move between items, down to go deeper into one.
- **Journey features:** columns are the steps in flow order. A branch (cancel, details, history) gets its own column right after the step it starts from, named `{step} › {branch}`. A step with its own sub-flow starts its column with its own User flow.
- **Status-driven features:** columns are statuses and the changes between them, in lifecycle order (`Active` → `Active → Inactive (pending)` → `Inactive` → …), matching the Transitions table. A status column stacks Screen spec (in that status) → Screen variants & edge cases → Component states. A transition column stacks the chain as the user lives it: source screen with a marker on the trigger → confirm → feedback → pending screen → cancel path back. Conditions (not eligible, cut-off window, role) get a column right after the status they change. The Overview flow is a state diagram (§6).

**Reflow after every edit.** Sections don't use auto layout, so a board that grows never pushes the boards under it. They end up overlapping, and a dev reading the canvas misses whatever is hidden. After any edit that changes a board's size (adding a table, row, card or state), reflow before reporting:
1. **Restack the column.** Sort the section's boards top → bottom and set each one's `y` to the previous board's bottom + 120.
2. **Grow the section** to its last board's bottom + its bottom padding (`resizeWithoutConstraints`).
3. **Push the lines below.** If the section now ends past the next line's top − 240, move that line and every line under it down by the same amount, together. Do the same sideways when a board gets wider: the columns to its right move right.
4. **Check the whole page.** Loop over the sibling boards in every section and the page's top-level nodes, and flag any two whose boxes intersect. Fix every hit, including ones you didn't cause, then take a screenshot of each section you touched.

- Breakpoints board with one section per screen and modal (§2c), in Scope order, each reading left → right across the breakpoints at real size. Components get a row only if their layout changes by size.
- Rows, not columns: the point is comparing one screen across sizes, side by side. Adding an item adds height, never width.

**Line 5 · Log** — Changelog alone, last.

**Headers and navigation**
- **Nothing sits on the bare canvas.** The team may view the file on Figma's dark canvas or a light one, so no text or label is ever placed straight on it: everything lives on a white board card or on a line bar. Never add a floating group label between boards (like "Shared rules · Used across items"); the board's own pill already says where it belongs. A kit legend or intro also goes on a white card.
- **Line header = ink bar** (above the line): fill `#141414`, an 8px left stripe in the feature colour, radius 12, padding 28 / 36. Mono `LINE {n}` in `#B5B5B5` + line name, 40 bold, white; on the same row, right side, mono 16 `#B5B5B5`: `↑ {up} · {next} →` → the question, 22, `#C9C9C9`. It carries its own fill, so it reads on any canvas colour.
- **No section headers.** Lines and Details columns get no header inside them: the line bar names the part of the talk, and each board's location pill names the line, the position and the item. Never draw canvas connector lines between sections; they cross boards and break when anything moves.
- **Say it once.** Each level names something new, never the same thing again: line bar = which part of the talk; location pill = where this board sits (line · position · item); board title = which page type; the question under it = why the board exists. No board label above boards (the Figma frame name is enough), no column header that repeats the pill, and no section title that repeats the line name. If two stacked headers say the same word, delete the lower one.
- **Headings always outrank what's under them.** A heading is never smaller, lighter, or greyer than the content it introduces: sub-headings are Google Sans SemiBold 20, ink, sentence case (`Screen variants`, `Field rules`, `Open questions`), with an optional grey note line (16, `#6B6B6B`) under them; Contents group headings are Geist Mono SemiBold 20 in the feature colour, with their items smaller and softer below. Grey is for meta and hints, never for a heading.
- Positions (`2 of 5`) are reading aids, not codes: same number on the Scope card, the Contents entry, the Details column, and the Sizes row.

**Spacing** — 240 between lines, 32 from a line bar to its boards, 120 between boards in a row or column. Everything top-aligned.

**Sections, naming and layers**
- One Figma Section per line, except Details (one per column). Sections are **transparent, no padding**: they only group boards in the layers panel. The white board cards and the line bars do all the visual work.
- Name board frames in the layers panel `{page type} · {item name}` (e.g. `Component states · Tag picker`), so Dev Mode and search find them. This name is not printed on the canvas. No page number.
- **Boards are white cards.** Every board frame is fill `#FFFFFF` at **95% opacity**, a 1px `#D6D6DA` outline, radius 16, padding 96 / 120, gap 48. Each board reads as its own sheet on any canvas, light or dark. The board header (§4.1) opens every board.
- **Frame budget: two levels.** Board card → content. Never nest a box inside a box inside a box: no white card wrapped around a screen preview (the screen itself is the frame: no fill change, no border), no panel around a single table, no filled section behind the boards. If you count more than two nested frames around any piece of content, remove the extra ones.
- Order the layers panel to match the canvas: lines top → bottom, sections left → right, boards top → bottom.
- One item per board. Never put two items' states on one board.

**Colours — white boards, one grey for information, the feature colour as an accent.**
- **Layers, back to front:** canvas (whatever Figma shows, light or dark; nothing sits on it) → line bar `#141414` / white board card `#FFFFFF` 95% with a `#D6D6DA` outline → info surfaces (notes panels `#E3E3E3`; context strips and table cells `#F1F1F4`; label cells `#E4E4E8`; grid lines `#E8E8EC`; table header ink `#141414` with `#E5E5E5` text) → screens at 100%, exactly as designed, with no border.
- The **feature colour** appears in exactly four places: the **8px left stripe** of each line bar, the **6px bar** beside every board header, the **location pill** dot, and the **item bars** on Breakpoints (white text on the feature colour). Nothing else is tinted by feature. Colours are vivid, not pastel.
- Feature colours, in order (a sixth feature starts again at Violet):

| Feature | Line stripe, header bar & item bars |
|---|---|
| 1 · Violet | `#7C3AED` |
| 2 · Blue | `#2563EB` |
| 3 · Teal | `#0D9488` |
| 4 · Magenta | `#DB2777` |
| 5 · Amber | `#D97706` |

- **Text on boards:** titles and headings `#141414`; meta, hints, and secondary labels `#6B6B6B`. On the ink line bar: title white, `LINE {n}` and nav `#B5B5B5`, question `#C9C9C9`. No lilac.
- **Colour means something, or it isn't there.** Every meaning gets its own hue, clearly apart from the others; never two meanings on neighbouring hues (teal next to green reads as the same tag):

| Meaning | Hue |
|---|---|
| Feature, markers, decisions, codes, references | violet |
| `New` | green |
| `Changed` | blue |
| `Reused` | pink |
| `TBD` | amber |
| Error | red `#B42318` |

  Everything else is black, white, or grey.
- Boards shared by several features use a neutral grey `#6B6B6B` stripe and header bar.
- **Accent never matches the product.** Markers, code chips, references and pills must stand apart from the screens they sit beside. If the product's own UI uses purple or violet, switch the whole accent family to a hue the product doesn't use (e.g. teal `#0F766E`, code chip `#0F5E58` on `#CCF0EB`, pill `#0F5E58` on `#E0F5F2`), and move any feature colour or tag that would then clash to another free hue. Record the choice in the project profile; one accent per file.
- Never make notes panels or tables pure white next to the screens: they compete. Never use a dark board; light boards keep long notes easy to read.

## 2c. Breakpoint sections — one item per section

On 05, every item that reflows gets its own section. Never stack a modal under a screen card.

- **Order:** `A` Full screen (the whole UI) first, then each overlay in the order it opens: `M1`, `M2`… (modal, sheet, drawer, popover).
- **Item bar:** a solid bar in the **feature colour**, radius 10, padding 16 / 24: kind label (`FULL SCREEN` / `MODAL`, Geist Mono 14, `#EDE5FF`) · item name (28 Semibold, white) · right side: where it opens from (Geist Mono 13, `#EDE5FF`). It's the divider you scan for on a long Breakpoints board, so it must pop; 80 gap between sections.
- **Same columns in every section:** the project's sizes (§0a), same widths, so a column reads top to bottom as one breakpoint.
- **Overlay preview:** the overlay instance at 100% on a 45% ink scrim, inside a frame of the breakpoint's real viewport size, fixed height (e.g. `M · 1280 × 800`). Never scale the frame or the overlay. If the overlay is taller than the viewport, show it the way the product does (capped with an inner scroll, or full screen) and let the frame clip; don't grow the frame. The overlay keeps its real width (e.g. 560); if it becomes full screen, show it full screen.
- **Card names:** size name + width, using the project's names (`Large · 1280`, `Mobile · 390`). Notes elsewhere cite `See Breakpoints › {item} › {size name}`.
- Skip a breakpoint card only if the overlay can't open there; leave the slot empty.
- **Every step of the flow gets breakpoints.** Each screen and each modal step in the 04 flow (step 1 and step 2 of a two-step modal, a declaration step, a confirm) has its own section, in its own row's Breakpoints board. A step with no master at a breakpoint gets one (ask first), never a skipped section.

## 2b. Screen size — every page

Every board states which size it's shown at, using the project's size set (§0a). On a one-size product the strip still says the size, and Other sizes is `—`.

- Put it in the layout as its own **context strip** under the board header: a divider grid, one cell per item (mono label + value). Never as a side note by the title.
- Cells: **Screen size** (`L · 1440 × 900`, or `All sizes` when the content doesn't change by breakpoint) and **Other sizes** (`See 05` / `Changes in 05`).
- 01 Screen spec adds these two cells to its existing strip: Condition · Entry · Related · Screen size · Other sizes.
- Component boards (02, 03, 03b) add a third cell, **Figma component**, holding the component's exact library path (`OrderHistory / Status`) in mono.
- Applies to 01, 02, 03, 04, 06, 08. 05 Breakpoints is the size reference itself; 07, 09, and 10 don't need it.
- The size must match a breakpoint on the Breakpoints board, written as name + size (`Large · 1280 × 800`), never an internal code like `B2`.

## 3. Tokens

Create these as Figma **color and text styles** (or variables) in the handoff library.

| Role | Value |
|---|---|
| Board | fill `#FFFFFF` at 95% opacity, 1px `#D6D6DA` outline, radius 16 |
| Line bar | fill `#141414`, 8px feature-colour left stripe, radius 12 |
| Section / canvas | transparent / not styled (nothing sits on it) |
| Notes panel | `#E3E3E3` |
| Info surface: strips, table cells / label cells / grid lines / table header | `#F1F1F4` / `#E4E4E8` / `#E8E8EC` / `#141414` |
| Screen preview | the master at 100%, no border, no added fill |
| Card tab | fill `#141414`, text white |
| Ink | `#141414` |
| Secondary text | `#4A4A4A` |
| Muted / meta | `#6B6B6B` |
| Placeholder text | `#9A9893` |
| Accent (markers, decisions, references) | `#7C3AED` |
| Code chip | text `#6D28D9`, fill `#E4D7FD` |
| Location pill | text `#6D28D9`, fill `#EFE8FE`, dot feature colour |
| Position chip | text `#4A4A4A`, fill `#EDEDF0` |
| Feature colour | see Colours (§2d) |
| Text on the line bar (title / `LINE {n}` and nav / question) | `#FFFFFF` / `#B5B5B5` / `#C9C9C9` |
| Error | `#B42318` |
| Decision fill | `#F3EEFF` |
| Scope tags (fill / text / dot) | New `#E3F5EA` / `#146C36` / `#22A55A` · Changed `#E6EDFF` / `#1D4ED8` / `#3B6EF0` · Reused `#FCE7F3` / `#9D174D` / `#DB2777` |
| TBD fill / dashed border / text | `#FFF4D6` / `#D9A21B` / `#7A5300` |

| Text style | Spec |
|---|---|
| Line bar title | Google Sans 40 / Bold / white |
| Board title (page type) | Google Sans 44 / SemiBold / −1% / 110% / ink, sentence case |
| Board question | Google Sans 20 / `#6B6B6B` |
| Location pill / Scope tag on a board header | Geist Mono 15 / SemiBold |
| Sub-heading (`Screen variants`, `Field rules`, `Open questions`…) | Google Sans 20 / SemiBold / ink, sentence case |
| Contents group heading / item | Geist Mono 20 SemiBold feature colour / Google Sans 16 `#4A4A4A` |
| Card tab title | Google Sans 18 / Medium |
| Body / conditions | Google Sans 16 / 145% |
| Quoted UI copy | Google Sans SemiBold, ink, same size as the text around it |
| Code chip, references | Geist Mono 14 / SemiBold |
| Marker numbers, meta | Geist Mono 13–15 |

**Fonts.** The handoff uses Google Sans (text) and Geist Mono (labels, codes). Another team's Figma may not have them: before building, `await figma.loadFontAsync(...)` each style you need. If either fails, use **Inter** for Google Sans and **Roboto Mono** for Geist Mono (both ship with Figma), keep every size and weight the same, and note the swap in the project profile. Never mix the two pairs on one file.

If notes use a non-Latin script, add a matching fallback font to every text style so line height stays at 145%.

**Board frame:** white card (fill `#FFFFFF` at 95% opacity, 1px `#D6D6DA` outline, radius 16); width Fixed 1920, but switch to Hug (min 1920) when a table needs more room; height Hug (min 1080); vertical auto layout, padding 96 top/bottom × 120 left/right, gap 48.

## 4. Handoff components

Look for the kit first, where the user said it lives (§0a, Locations): components named `Handoff / …` in that file or an enabled team library. Use them as they are. If there are none, build them once in the kit location (default: a `Handoff kit` page in the boards' file) before the first board (tokens from §3, fonts from the profile), so every board uses instances and a later style change is one edit.

1. **Board header** — opens every board, nothing else beside it:
   - A **6px bar** in the feature colour, radius 3, on the left, as tall as the header; gap 20.
   - **Location pill** (Geist Mono SemiBold 15, `#6D28D9` on `#EFE8FE`, radius 999, padding 5 / 14 / 5 / 12, an 8px dot in the feature colour) naming where the board sits: `Overview`, `Scope`, `Scope · Shared rules`, `Details · 1 of 2 · Screen name`, `Sizes`, `Log`. On Details boards the item's Scope tag sits right after it, gap 10, at exactly the same size (30 tall, 15 text).
   - **Title:** the page type in sentence case (`Screen spec`, `Screen variants & edge cases`, `Component states`), Google Sans SemiBold 44, ink.
   - **Question** under it, Google Sans 20, `#6B6B6B`: what the board answers (`What's on the screen and what each action does`).
   - No eyebrow, no side note, no item-name line: the pill already names the item.
2. **Divider grid** (tables and context strips only; cards are separate, §2) — auto layout (Grid, or vertical stack of horizontal rows) with **1px gap**, frame fill `#E8E8EC`, 1px `#E8E8EC` stroke, radius 12, **Clip content** on. Each cell carries its own fill (`#F1F1F4`; label cells `#E4E4E8`), so the 1px gaps read as hairlines. No strokes on cells.
3. **Dark header cell** — fill ink `#141414`. In tables: padding 18/24, `#E5E5E5` mono label. On cards: a Hug-width tab, white text, padding 12/24, top corners radius 12, title only (18 Medium); the meta sits outside it on the right of the header row in `#6B6B6B`.
4. **Label cell** — fill `#E4E4E8`, padding 24; title + muted condition.
5. **Instance cell** — fill white, padding 24, width Fixed = component's real width + 48, vertical auto layout: preview slot → mono state name. The slot holds the master instance at 100%; when there's no instance yet, it holds the default placeholder.
6. **Corner cell** — dark, mono `V ↓ · S →`.
7. **Default placeholder** — the standard empty slot for every preview (instance cells, screen spec, flow nodes, edge-case cards):
   - Frame, radius 6, no stroke, **Clip content** on, fill white
   - Stripes: 45° pattern of 10px bands, ink `#141414` at **3.5% opacity**, every 20px
     - **Preferred: pattern fill.** Make a 20 × 20 tile with one 45° band, and use it as the frame's fill (Pattern fill, or Image fill → **Tile**). Covers any size; survives resize and swap.
     - **Fallback: rotated stripe group.** Its W and H must be **≥ the frame's diagonal** (√(w² + h²); 1220 × 900 → ≥ 1520 × 1520), centered on the frame both ways, Clip content on. A smaller group only covers one corner after rotation.
     - Check: stripes reach all four corners. A plain white triangle means the group is too small.
   - Centered label: Geist Mono 13–15, `#9A9893`, lowercase, saying what goes there (`state capture`, `screen capture`, `screen capture · 1440 × 900`)
   - Same aspect ratio as the master it stands in for, so nothing shifts on swap
   - Use **Swap instance** to replace it with the master instance; never hand off a board that still has placeholders
8. **Quoted UI copy** — not a component and not a frame: a text run inside the note or cell text, “exact copy” in curly quotes, Google Sans SemiBold 600, ink `#141414`, same size as the text around it. Never boxed, filled, or outlined (that reads as a real button), and never accent (accent is for codes).
9. **Marker** — pill, fill accent, white Geist Mono 14, text = number (`1`). A component with a text property.
10. **Surface tag** — names where a message or step appears: emoji + mono uppercase label in a **light pill** (fill `#FFFFFF`, 1px `#D4D4D8` outline, ink `#141414` Geist Mono SemiBold 12, +4%, radius 999, padding 3 / 10 / 3 / 8, gap 6). Light so it reads on every background it sits on: the ink header of a flow node, the grey table cells of 09. Never ink-filled (it vanishes on an ink header) and never accent or a tag hue (those already mean something).
    - One emoji per surface, always the same one in the file: `❓ CONFIRM` · `🪟 MODAL` · `📄 SHEET` · `📢 BANNER` · `✏️ INLINE` · `🍞 TOAST` · `🔔 PUSH` · `✉️ EMAIL` · `💬 SMS` · `🌐 BROWSER`. A surface not on this list gets one plain, obvious emoji, added to the `Handoff profile` so it's reused.
    - Emoji text uses the system emoji font (Figma falls back to it automatically); set the emoji at 13 so it lines up with the 12 label.
11. **TBD tag** — the only way to show missing info. Inline auto layout, padding 1/8, radius 4, fill `#FFF4D6`, **1px dashed** `#D9A21B` stroke, text `TBD` in Geist Mono SemiBold 13, `#7A5300`, +4% tracking, never wraps. Stands alone in the cell or bullet; the actual question and who to ask go in Open questions. A short hint may follow in `#7A5300` (`TBD` which users). Never write TBD as plain or muted text, and never use the TBD colors for anything else.
12. **Scope tag** — mono pill with a dot, so it reads as a handoff annotation and never as a badge in the real UI: radius 999, Geist Mono SemiBold 12, +2%, padding 3 / 10 / 3 / 8, a 7px dot (15 text, 8px dot on board headers). `New` (fill `#E3F5EA`, text `#146C36`, dot `#22A55A`) · `Changed` (`#E6EDFF` / `#1D4ED8` / `#3B6EF0`) · `Reused` (`#FCE7F3` / `#9D174D` / `#DB2777`). Used on Scope cards, Contents, and Details board headers.
13. **Spotlight** — a component placed over an existing screen (at 100%) on Scope, variant `type = new | changed | reused`, radius 6, with a small label under the outline (white Geist Mono SemiBold 12 on the outline colour, radius 999).
    - **New / Changed:** a **solid** 2px outline (`#15803D` for NEW, `#1D4ED8` for CHANGED) and a white overlay at 72% across the rest of the screen, so the old UI fades and only the new or changed part stays sharp. Label `New` / `Changed`. These mean "build this".
    - **Reused:** a **dashed** 2px outline in `#DB2777` (dash 6, gap 4), no overlay of its own, around an existing component placed in this screen as is. Label `Reused · {component name}`, the name exactly as in the library. It means "use the existing component, don't rebuild it". Only on a screen that is new or changed; a reused item alone gets no spotlight.
    - One spotlight per change or reused component; brand-new items get none. Several reused components in one area → one outline per component, never one outline around all of them.
14. **Code chip** — the one style for everything that is code: API / enum values, backend codes, `{code}` in messages, Figma component paths, variant properties (`Type=Primary`). Geist Mono SemiBold 14, `#6D28D9` on `#E4D7FD`, radius 4, padding 2 / 8, never wraps, Hug width. Never a second purple style.
15. **Reference** — anything that points elsewhere in the file: `↓ Details › Screen name`, `See Field & validation › Amount`, `See States › Missing fields`, `See Breakpoints`, `Next → Hover`, and marker numbers cited in notes. Geist Mono SemiBold, violet `#7C3AED`, no fill. Dates, times, and format examples stay ink: they're data, not links. A reference always sits after words that say the rule; it never replaces them.
16. **Position chip** — `1 of 2` in Contents and on Scope cards: Geist Mono 12, `#4A4A4A` on `#EDEDF0`, radius 999, padding 3 / 10.

## 5. Page types

Name each board frame `0# Page type — {Screen name}`.

| # | Page | Structure | Internal refs (never shown) |
|---|---|---|---|
| — | Contents | List of every line and, under Details, every item with position (`2 of 5`), kind, and Scope tag. Each name links to its section. Sits on Overview, before the User flow | — |
| — | Scope | One card per item the feature touches (screens and overlays in flow order, then components, REUSED last): header tab = item name, meta = position + Scope tag (`NEW` / `CHANGED` / `REUSED`) · preview: the item at 100%; an existing screen gets a **spotlight** · notes: What changes (one line) · Where (`↓ Details › {item}`, or `No Details column · use as is` for REUSED). Footnote explaining the spotlight colors | — |
| 01 | Screen spec | Context strip (Condition · Entry · Related · Screen size · Other sizes) → **every step of the item side by side** (a two-step modal shows Step 1 and Step 2), each at 100%, a marker on every element → **element table** beside it (`#` · Element (quoted copy + type) · Rules: `condition → result` lines, each ending with `See States › {state}` when it has one), one row per marker, numbered on through all steps → **List controls** block (list screens only) | `E#`, `L#` |
| 02 | Variant × state matrix | Rows = variants (with "when"), columns = states (with trigger). Columns sized to the component's real width; board grows if needed (§2) | `V#`, `S#`, `V#·S#` |
| 03 | Component states | Horizontal state cards (§2): header state name + `Next →` · preview at real width · note: the trigger and result in one line (`Pointer over → darker fill`) | `S#` |
| 03b | Component types & variants | Up to three blocks. **Component types:** horizontal cards (§2), one per type / item kind (e.g. Primary, Secondary, Ghost, Destructive, Icon only): header type name + `States: Default – Disabled` (links to Component states) · preview at real width · Use when · Don't use · Property chip (`Type=Primary`, matches the Figma variant property). **Option sets** (when the component has one): per set, the **picker on the left** (the real component, with a marker on each option) and its **Option data** table on the right with the same numbers (`#` · Option (quoted) · API value · the set's details: who can pick it, set by system, sub options it opens…). A set whose options depend on a parent (platforms per type) gets one side-by-side block per parent value. **Option variants** cards only when picking an option changes the layout in a way the block above can't show | `T#`, `OP#` |
| 04 | User flow | Flowchart (§6) | `F#`, `E#` |
| 05 | Breakpoints | **One section per item, never mixed** (§2c). Each section = item bar + one row of cards, one per size in the project's size set (§0a), each at the breakpoint's real size, 100%, never scaled (§2): header breakpoint name + range · preview frame = real viewport size · Layout change · Hidden / moved, with a marker where the layout reflows | `B#`, `M#·B#` |
| 06 | Screen variants & edge cases | Every rule that changes what the user sees, as a picture. Card rows (§2), one row each, the board grows: **Screen variants** (one per version: role, record status, plan, data), **Rule states** (one per rule: a type changed, an option picked, a field missing), then **Edge cases**, ending with **Longest content**. Each card: header = the state's name · the **whole** screen or modal at 100% with real data in every field (never a crop of the part that changes) and an accent outline on what changes · note = the rule as one `condition → result` line, the same words as its Screen spec row, plus `See Messages › {title}` when it shows a message | `SV-0#`, `EC-0#` |
| 07 | Changelog | Version · Date · Affects (board › item, by name) · Change. No status column; release status is tracked outside the board | `v#.#` |
| 08 | Field & validation | **One table per item, every step together**, with a full-width group row per step: `#` · Field (name + a picture of the real field) · Default · Required · Validation (Checked when · Message · CTA effect, when the source gives them) → horizontal **field state cards** (§2) showing each state and each failing rule in the real component | `FV-0#`, `FV-0#·S#` |
| 09 | Messages & errors | One **message table** per surface, in this order: Confirm → Error modal → Banner → Inline → Toast. Columns: Surface tag · Trigger · Title · Message · Error code · Actions → result. Preview column holds the real instance at 100% | `MSG-0#` |
| 10 | Status & formats | **Status previews** (one card per status, in lifecycle order: header tab = label shown, meta = API value · preview of the component or screen in that status · Shows · Actions) → **Status lifecycle table** (Status shown (quoted) · API value · Maps from (backend codes) · Meaning · Set by · Allowed actions · Not allowed) → **Transitions table** (From → To, as quoted status labels · Trigger (element name) · Who · Confirm (message title) · Feedback (message title) · Pending status shown · Takes effect · Can cancel? → back to) → **Time windows table** (Window · What's blocked or changed · What the user sees) → **Formats table** (Value type · Format · Example · Empty value) | `ST#`, `ST#→ST#`, `TW#`, `FMT#` |

**Action rules live in the button's row.** Every CTA, link and icon button gets its enabled / disabled / hidden conditions and its on-click result as lines in its Screen spec row. A CTA with no condition still gets an on-click line. `TBD` when the source is silent.

**01 List controls block** — divider grid, one row per control: Search (fields searched, match type: exact / prefix / fuzzy, min characters), Filter (options, multi-select, `Select All` / `Clear`, ranges), Sort (options, default, direction labels as shown in the UI), Reset / Apply behavior, Pagination or scroll area (page size, inner scroll), Selected summary (e.g. `2 Selected`). No results → cite the 06 card.

**03b option set rules:**
- **The picker and its table side by side.** Left: the real component at 100%, in the state that shows the set (for a picker that opens sub options, with one option picked so its sub row shows), a marker on every option. Right: the Option data table, one row per option, the same numbers. The Option data table never stands alone.
- **One block per parent value.** When the options depend on another choice (platforms per type), repeat the block for each parent value, each with the picker set to that value, so the picture is always beside the rows it explains.
- **Values the user can't pick still get a row** (set only by the system, hidden for a role), with `—` in `#` and the reason in the who-can-pick column.
- **Values only.** Who sees an option, what picking it opens or clears: the condition is a line in the component's Screen spec row. The table holds values: label, API value, sub options, who can pick it, set by.
- **Option variant cards** are only for layouts the side-by-side block can't show (e.g. two options open at once, each with its own sub row). Each card's note is its rule line.
- The 01 element note for that component keeps only the selection rules (required, single / multi, default) and cites `See Component types & variants › {component}`. It does not hold the option list.
- Option labels and sub option labels are exact UI copy, quoted. Descriptions may be in the team's language.

**08 rules:**
- One row per rule, not per field: a field with min, max, and required has three `FV` rows.
- **Checked when** is one of: `On type` · `On blur` · `On submit` · `On select` · `Server`.
- **Message** is the exact inline copy, quoted, or `—` if the rule only disables the CTA.
- **CTA effect** names the button and its state (`Add` button disabled).
- Conditional rules state their condition first (`Field optional → no required marker`).

**09 rules:**
- Every message shown anywhere in the feature appears here once, with its exact copy, quoted. Boards elsewhere cite it by title: `See Messages › {title}`.
- Error modals that share a title but differ by cause get one row per cause, each with its own trigger and error code (e.g. one generic error title used for several backend reasons).
- **Actions → result** lists every button on the surface (`Keep editing` → closes modal · `Discard` → discards draft, back to list).
- Toasts add duration and dismiss behavior only if the source gives them; otherwise `TBD`.

**10 rules:**
- **Show each status before describing it.** The Status previews row comes first: every status, including pending ones, as a card with the real component or screen in that status. The table below explains them.
- **API value and Maps from** come only from the source (API spec, backend ticket, existing code). Several backend codes collapsing into one label is common; list them all in Maps from. Missing → TBD tag + Open question asked to backend.
- Statuses belong to the **record** (e.g. an order, a request, a payment), not the UI component. Component states stay on 03.
- **Set by** names the actor: user role, customer, system job, or time (expiry).
- **Allowed actions** names the actions from Screen spec (`Cancel` button), so a rule like "can't cancel unless `Pending` and not expired" lives here, not as a loose note.
- **Pending is a status.** When a change doesn't apply immediately (scheduled, needs approval, next day), the in-between state gets its own status row and its own screen. Never describe it only in a toast.
- **Takes effect** is exact and comes from the source: `Immediately`, `Next day 00:00`, `After approval`. Otherwise `TBD`.
- **Can cancel?** names the cancel action (`Cancel request` button), its confirm message title, and the status it returns to. If cancelling isn't allowed in some window, name the window.
- Time windows state the exact range and timezone if the source gives it, and show the blocked control in its disabled state on the screen.
- Formats only list types the source shows (currency, units, percent, date, time, phone, ID masks). Never add a format the design doesn't use.

**Open questions block** — on 01, 03, 03b, 06, 08, 09, and 10, at the bottom of the board: sub-heading “Open questions” + divider grid `About · Question · Asked to` (About = the item's name), one row per `TBD`. Delete the block when it's empty.

## 6. User flow

- Draw directly on the board frame. No container card.
- **Start on the entry page, at the button.** The first node is a Screen node holding the real page the flow starts from (the list, dashboard, or detail page with the trigger on it), with a marker on the element that starts the flow. The first arrow leaves from that marker. Never start with a text pill that only names the button.
  - **Look for the entry page first.** Before drawing, search the file for the page the trigger lives on (the design page, the feature's other frames, linked prototypes). If it exists, use it as an instance. If you can't find it, ask the user where it is. Only if they confirm it doesn't exist, use the default placeholder labelled `entry page · {name}` and add an Open question.
  - A Start pill is only for flows with no screen to start from (deep link, push notification, email link, scheduled job). Its label names that source.
- **End on the screen the user lands on.** The last node is a Screen node of where the user ends up (e.g. the entry page again, with the toast and the updated row visible), tagged `END` in its footer. No End pill with text like "saved · modal closes". Use an End pill only when the user leaves the product.
- **Every screen in the flow that exists in the file appears as a Screen or Overlay node** with its instance. Text-only nodes are for decisions and for things with no UI.
- **Max 9 nodes, 12 arrows.** Anything longer → split into an overview board and detail boards.
- **Flow node component**, variant `type = screen | overlay | decision | error | terminal`:
  - **Screen:** vertical auto layout, 1.5px ink stroke, radius 12, Clip content → dark header 44 (screen name) → preview area (fill `#F1F1F4`, padding 24, holds the master instance at 100%; node width = screen width + 48) → 1px divider → footer (Geist Mono 13 muted, padding 12/24) for the sub-line in words (`Empty queue → back to list`). The sub-line never sits inside the preview area.
  - **Overlay:** same as Screen, with a surface tag (`🪟 MODAL`, `✉️ EMAIL`, `🌐 BROWSER`…, light pill, §4) in the ink header and its host screen scrimmed in the preview. Name its message title in the footer when it has one.
  - **Decision:** diamond, fill `#F3EEFF`, accent stroke, the question in a few words (`Valid?`). **At most 3 exits, every exit labelled** (`YES`, `NO`, or the outcome); by convention yes leaves to the right, no below. More than 3 outcomes → split into two decisions. Decisions are the only accent shapes in the flow.
  - **Merge:** a small ink dot (8px) where branches rejoin before the next node, so two arrows never land on one point.
  - **Error:** white, `#B42318` dashed stroke (dash 5, gap 4). Footer names the message it shows.
  - **Terminal:** pill; Start = ink outline, End = ink fill. Only for the no-screen cases above.
- **Arrows start at the trigger.** Every arrow caused by a user action leaves from the marker of the element that causes it, drawn on the source screen's preview (marker `2` on the `Next` button), not from the node's edge. This includes arrows into a decision (`Next` → "valid?") and return paths (`Back`). Draw the arrow's first segment above the node so it visibly touches the marker. Only arrows out of a decision, or caused by the system, leave from a shape's edge.
- **Nodes are as big as the screen.** Every Screen / Overlay node holds its master at 100%, so a page node is ~1330 wide and a modal node ~570. The board grows to fit (Hug). Plugin API: a nested instance inside a kit Flow node can't be resized, so detach the node, swap its preview slot for the master, and set the node width to the screen width + 48.
- **Arrows:** vector lines, 1.5px stroke `#6B6B6B`, arrow end cap. Main path on one horizontal axis; errors drop straight down from decisions; return paths dashed muted (dash 5, gap 4). Keep arrows on a layer below the nodes.
- **Connector rules (from diagram-design; every flow and state board):**
  1. **Orthogonal only, rounded elbows.** Every bend is a right angle with corner radius 8. No diagonals. An arrow leaves and enters a node perpendicular to the edge it touches, at least 8px from a corner, never sliding along a border.
  2. **Labels never touch their arrow.** A visible 6–10px gap between the label frame and the stroke; beside the line on vertical segments.
  3. **No stacked arrows.** Two arrows never share a path: keep parallel routes 12px or more apart. Where two must cross, add a small hop (a 6px arc) on one so each stays traceable.
  4. **One attach point per arrow.** Several arrows on the same node edge get their own points, spread evenly, 12px or more apart.
  5. **No arrow behind a node that isn't its start or end.** Reroute around it. Only when no other route exists: dashed, label at the visible end, and no arrowhead on the node it passes.
  6. **Labels sit in open space.** A label frame never overlaps any node, only the board.
  - **Check by script, not by eye.** Walk the board: flag any arrow segment that isn't horizontal or vertical, any two segments that overlap, any label frame that intersects a node or touches its stroke, and any two arrow ends on the same point. Fix every hit and re-run.
- **Arrow labels:** Geist Mono 13 uppercase, short (about 14 characters; `NEXT · MODAL`, `SUBMIT`, `BACK`, `EXTERNAL`), on a small frame filled white (the board color) so it masks the line, centred on its segment, in open space between nodes. Gaps between nodes ≥ 80px. Add the navigation type when it isn't the same page: `· NEW TAB`, `· MODAL`. Never vertical text.
- **State diagram variant** (state layout): nodes are statuses (Screen node type with the status screen inside, titled by the status label). Arrows are transitions labelled `{trigger} [{condition}] · {takes effect}` in words, leaving out parts the source doesn't have (e.g. `DEACTIVATE · NEXT DAY`, `APPROVE [ADMIN]`). Cancel-request arrows are dashed and return to the previous status. Time windows show as a small muted tag on the arrow they block (`23:50–24:00`).
  - **Start and end:** a small filled ink dot before the first status; a ringed dot after a final status. Self-loops (an action that keeps the status) curve above the node.
  - **"From any status" is one note, not many arrows:** e.g. `Any status → Suspended on fraud flag`, as a muted tag near the Suspended node.
  - **Lifecycle layout** when one record moves through 4–5 main statuses and its waits, retries and endings matter as much as the happy path: main statuses left → right on one row; waiting or pending statuses in a band below; final outcomes (cancelled, failed, closed) in their own band at the end, each its own node. At most 9 statuses and 10 transitions; past that, split the overview from the detailed rules.
  - More transitions than twice the number of statuses → it's two flows; split them.
- Legend: a horizontal strip at the bottom with a hairline above it, never inside the flow. It covers every node type, line style and navigation label used on the board, and nothing else.

## 7. Keeping it in sync

- Change the master → the boards update by themselves. Re-read only the notes for that item.
- Every design change gets a row on 07 Changelog, listing the affected boards and items by name.
- **Pre-handoff checklist:**
  - [ ] Every preview is an instance of a master, none detached
  - [ ] Every preview is the master at 100% — nothing scaled with K, nothing resized, no thumbnails; only 05 crops the height to the viewport
  - [ ] Table columns match the component's real width; nothing hugged, cropped, or tidied
  - [ ] Every detail a note mentions is visible, with a marker
  - [ ] Every marker number appears on exactly one row or bullet, and vice versa: no repeats, no gaps, reading order
  - [ ] Marker pills in tables show as full circles (`#` column wide enough, Clip content off)
  - [ ] No internal code on the canvas: no `E#`, `FV`, `MSG`, `ST`, `SV`, `OP`, `B#`, no page numbers in pills or labels, nothing in brackets after a name
  - [ ] Every gap uses the TBD tag (amber, dashed), never plain text, and has a row in Open questions
  - [ ] Every condition is a `condition → result` line in its element's Screen spec row, beside the screen; no separate Rules board, no rule numbers or tags; no invented rules; every `TBD` is listed in Open questions
  - [ ] Every rule that changes what the user sees has a state card, and the card's note is that rule in the same words
  - [ ] Markers number only this board's elements or values; nothing else on the board is numbered
  - [ ] Every state card shows the whole screen or modal at 100% with real data (no crops, no `…` placeholders), and its note says the rule in words, not just a reference
  - [ ] Every table that explains a picture sits beside it (UI left, table right, same numbers)
  - [ ] Every rule from the source / designer notes is in exactly one §1b bucket, and cited by name elsewhere
  - [ ] Every CTA's Screen spec row has its enabled / disabled / hidden and on-click lines
  - [ ] Hover messages are drawn only when the product draws them; a browser `title` is described in words
  - [ ] Every list screen has a List controls block (search fields + match type, filter, sort default, scroll / paging)
  - [ ] Every input has 08 rows for each rule, with exact inline copy and CTA effect
  - [ ] Every toast, banner, modal, and inline error in the feature is on 09 once, with exact copy and actions
  - [ ] Each status of the main record is on 10 with a preview card, its API value, the backend codes that map to it, who sets it, and which actions it allows
  - [ ] Component boards show the Figma component path; API values and backend codes come from the source, never invented
  - [ ] Every transition has trigger, confirm, feedback, takes effect, and cancel path; pending states have their own status row and screen
  - [ ] Row structure matches the feature: single row, journey (one row per step), or state (one row per status and transition)
  - [ ] Every option set has Option variant cards on 03b with real previews; no options documented as a text-only table
  - [ ] Every version of the screen (role, status, plan, data) has an `SV` card on 06 saying what differs from Default
  - [ ] Edge cases cover stale / conflict, maintenance, and limit reached when the source mentions them
  - [ ] Every board (except 05, 07, 09, 10) has a Screen size strip matching a breakpoint on Breakpoints (name + size)
  - [ ] Flow starts on the entry page (real instance) with a marker on the trigger; file searched for it, or the user asked
  - [ ] Flow ends on the screen the user lands on; no text-only Start / End pills when a screen exists
  - [ ] Every user-action arrow visibly starts at its trigger marker, and names the navigation type when it isn't the same page
  - [ ] Flow and state boards pass the connector rules (§6): orthogonal with rounded elbows, no stacked or shared arrows, a hop at each crossing, no arrow behind a node it doesn't connect, labels 6–10px off their line and clear of every node
  - [ ] Every decision has 3 exits or fewer, each labelled; every state transition is labelled with its trigger
  - [ ] Placeholder stripes reach all four corners (none half-white)
  - [ ] Cards on every board sit in one row, none wrapped; board width hugs
  - [ ] On wide boards, tables, strips, and Open questions stay 1680 wide, left-aligned
  - [ ] No table content is cut off: text wraps, chip rows wrap, headers fit on one line, no table runs past its frame
  - [ ] Cards in flow order; `Next →`, `States`, and `See …` references updated to match
  - [ ] No text layer has Fixed height or is clipped (resize a card narrower to test)
  - [ ] The text-clipping script (§2 Tables) ran clean: no text box outside its parent, no `NONE` / `TRUNCATE` / ending truncation, no auto-width text in cells or notes
  - [ ] Every button and label in every screen preview reads in full, exactly as in the source frame (none sliced like “Vie”)
  - [ ] UI copy is quoted text everywhere; no boxed UI chips left
  - [ ] No text on the bare canvas: every label is inside a white board card or a line bar (view the page on a dark canvas to test)
  - [ ] The four locations (boards, kit, design system, screen components) confirmed separately; nothing written to the design system or to a file the user didn't name
  - [ ] Project profile (§0a) filled and saved as `Handoff profile` on the sources page; nothing carried over from another project (sizes, names, examples)
  - [ ] 05: full screen and every modal in separate sections, same size columns (the project's size set)
  - [ ] 05: every preview frame measures its real size at 100% (the widths in the profile); nothing scaled. One-size product: no Sizes line at all
  - [ ] 05: every frame is fixed at the viewport height with Clip content on; no card shows the full page or grows taller than the others
  - [ ] Five lines top → bottom: Overview (Contents + User flow) → Scope (Scope board, then Shared rules) → Details (one column per item) → Sizes (one row per screen; skipped for a one-size product) → Log
  - [ ] Every Scope item that isn't REUSED has a Details column and (if it's a screen or modal) a Sizes row, in the same order with the same position (`2 of 5`)
  - [ ] Each Details column stacks its boards top → bottom (screen: spec → types & variants → variants & edge cases → field & validation → messages; component: types & variants → states → matrix)
  - [ ] Every line bar links up and on (`↑ · →`); one feature colour per feature, used only on the line stripe, header bar, pill dot, and Breakpoints item bars
  - [ ] No repeated headers: no section or column header rows, no board labels above boards; each board = header bar + location pill (+ Scope tag on Details) + sentence-case title + question
  - [ ] Every board is a white card (`#D6D6DA` outline, radius 16); sections are transparent; no more than two nested frames around any content
  - [ ] Card-level Scope tags sit in the card header row, never as a `Scope` section in notes
  - [ ] Tags read clearly apart: New green, Changed blue, Reused pink, TBD amber; on Details headers the Scope tag is the same size as the pill
  - [ ] One code-chip style for every code (violet on `#E4D7FD`); every reference (`See …`, `↓ Details › …`, `Next → …`) in violet mono; no second purple style
  - [ ] Boards `#FFFFFF` at 95%; notes panels `#E3E3E3`; strips and table cells `#F1F1F4`; grid lines `#E8E8EC`
  - [ ] No border on any screen preview
  - [ ] No marker sits on a label or is cut at a frame edge; frames holding marked screens have Clip content off
  - [ ] Existing screens on Scope show a spotlight on what's NEW (green, solid) or CHANGED (blue, solid), and a dashed pink REUSED outline, named, on every existing component the screen uses as is
  - [ ] Row headers show `from` / `next` / `branches`; no connector lines between rows
  - [ ] Icon, badge and tag tables show real component instances (or crops of them), never glyphs or redrawn icons
  - [ ] The overlay-on-host view appears once (Breakpoints, flow), not as a thumbnail on every card
  - [ ] A `Longest content` state exists and holds up at every breakpoint
  - [ ] Every screen and modal step in the flow has its own Breakpoints section
  - [ ] New tabs, pages and sections are named as new; the existing ones they sit beside are described as unchanged
  - [ ] Every value set has a details table (API value, source, who sees it, meaning or mapping), not just the list of values
  - [ ] No two boards, sections or lines overlap anywhere on the page: every column was restacked (120 apart) and every line below moved after the last size change
  - [ ] No placeholders left
  - [ ] No side notes by titles, no sign-off or status blocks
  - [ ] Changelog row added, listing affected items by name

## 8. Don't

- Grow a board (add a table, row, card or state) and stop there. Boards don't push each other, so reflow the column, the section and every line below (§2d), then check the page for overlaps.
- Invent rules, conditions, edge-case handling, validation ranges, error codes, or copy. Write `TBD` and ask.
- Paragraph notes. One `condition → result` line per rule.
- Put rules on a separate board, or number them (`Rule 3`). Write each rule beside the UI it changes.
- Leave a state card with only a reference (`Spec › 4`) as its note, or crop it to the part that changes. Whole screen at 100%, rule in words under it.
- Add a flow board inside a Details column that redraws clicks the User flow and Screen spec already cover. Each click is a line in its button's row; the journey is the User flow.
- Put a table under the picture it explains, or a picture far from its rows. UI left, table right; repeat the picture per group in a long table.
- Leave rules as floating sticky notes next to screens. Put each one in its §1b bucket.
- Repeat the same rule on several boards. Write it once, cite it by name.
- Paraphrase messages. 09 holds the exact copy in quotes.
- Document options, variants, or types as a text-only table. Show the component in each variant, then list the data under it.
- Put a long option list inside a 01 element note. It goes on 03b as the picker + Option data, side by side.
- Mix whole-screen variants (`SV`, on 06) with component variants (`V`, on 02 / 03b).
- Mix record statuses (`Pending`, `Expired`) into component states (Hover, Disabled). Statuses go on 10.
- Show a scheduled or pending change only as a toast. The pending state is its own status with its own screen.
- Force a status-driven feature into numbered steps. Use the state layout.
- Box UI copy in a white or outlined chip, or write it plain with no quotes. Write it as quoted SemiBold text: “Other”.
- Copy, duplicate, detach, flatten, or export screens onto boards.
- Scale or resize a preview. Every screen is 100%; the board grows.
- Put a grey outline or hairline around a screen preview.
- Centre a marker on a button or label, or let a parent frame clip it.
- Crop or cut off the rows, headings, footers, disabled CTAs, or inline errors a note refers to.
- Number markers differently from their rows.
- Reuse one marker number for two rows, or let a marker pill be cut off by a narrow `#` column.
- Write missing info as plain or grey text (`TBD — confirm with design`). Use the TBD tag, so gaps stand out from real data at a glance.
- Print internal codes on the canvas (`E1`, `FV-11`, `MSG-03`, `ST1`, `B2`, `08 ·`), or add them in brackets after names. The team reads names.
- Hide real dev names to keep the board "clean". API values, backend code mappings, and the Figma component path stay; only our internal codes go.
- Describe statuses only in a table or a code block. Show each status as a preview card first.
- Use grey boxes, icons, or "image here" text for empty slots. Use the default placeholder.
- Use a rotated stripe group smaller than the frame's diagonal. Use a pattern fill.
- Shrink, hug, crop, or tidy instances to fit a table column. Widen the column, and the board if needed.
- Table rows for states, breakpoints, or edge cases (huge empty note cells). Use horizontal cards in one row.
- Fixed-height text frames or truncated notes. Text frames hug.
- Order boards by page number (01, 02, 03…). Follow the five lines: Overview → Scope → Details → Sizes → Log.
- Put the user flow or the Changelog anywhere but their own lines: the flow sits on Overview with Contents, the Changelog alone on the last line.
- Lay a Details item's boards side by side, or mix two items in one column. One column per item, boards stacked top → bottom.
- Put Breakpoints inside Scope or inside a Details column. It's its own Sizes line, one row per screen.
- Show an existing screen on Scope without a spotlight. Fade the old UI, outline what's new or changed, and mark components used as is with the dashed Reused outline.
- Let a table or context strip stretch to the full width of a wide board. They stay 1680.
- Let anything in a table get cut off: clipped text, chips running past the cell, a table wider than the board, or headers wrapping onto two lines. Wrap the content, widen the Fill column, or split the table.
- Add a narrow link column in accent mono. Use group rows, or say it in words in the row.
- Wrap cards onto a second line inside a board (3 + 3, 4 + 3). Cards stay in one row and the board grows.
- Order Details columns differently from the Scope cards. Same order, same position number.
- Put a branch (cancel, details, history) inside a step's column. It gets its own column right after the step it starts from.
- Draw connector lines across sections. Use Contents, the flow, and the location pills.
- Scale down breakpoint previews on 05. They are real size, 100%; the board grows.
- Let a breakpoint frame hug the page's full height. It's the viewport, fixed height; the full page lives on Screen spec.
- Stack modal or sheet previews under a screen's breakpoint card. Give each overlay its own section on 05.
- Show a modal or toast floating alone with no host screen behind it.
- Cards in random order.
- Join cards into one shared grid with 1px gaps. Rows blur together and short cards leave grey gaps. Use separate cards with a 24 gap.
- Give cards a fill or a grey outer stroke of their own, or a full-width header bar, or put the meta inside the header tab. Cards sit straight on the white board: an ink title-only tab on the left, meta on the right, the 2px ink rule, the preview centred, and a `#F1F1F4` notes panel.
- Leave a board without its screen size, or put the size next to the title.
- Start a flow with a pill that names a button ("Add …") when the page with that button exists. Start on that page.
- End a flow with a text pill when the user lands on a real screen.
- Cramp flow nodes when the board has room.
- Draw diagonal arrows, let two arrows share a path or an attach point, or run an arrow behind a node it doesn't connect.
- Draw flow arrows from a screen's edge when a specific button triggers them, or omit `NEW TAB` / `MODAL` when navigation isn't same-page.
- Spec what Dev Mode already shows (spacing, size, color, tokens, props).
- Add side notes or meta lines next to board titles.
- Add sign-off, owner, status, or platform blocks, including a Status column on the changelog.
- Use phone frames for desktop products, or desktop sizes for a mobile app. Sizes come from the project profile.
- Carry sizes, names, colours, or example content over from another project. Every project gets its own profile (§0a).
- Use accent anywhere except markers and decision nodes.
- Tint boards by feature or by line, use a dark board, or use lilac for secondary text. Boards are white; the feature colour is only the line stripe, header bar, pill dot, and Breakpoints item bars.
- Make a heading smaller or greyer than the items under it. Headings lead: ink or feature colour, semibold, a size up.
- Stack headers that repeat each other (line `Sizes` → section `Sizes · every screen` → label `Responsive breakpoints` → board `BREAKPOINTS`). One header per level, each naming something new.
- Nest frames: board → white card → screen, or a filled section behind white boards. Keep two levels (board card → content), and let the screen be its own frame.
- Put any text or label straight on the canvas, or fill the sections behind the boards. Text lives on a white board or a line bar, so it reads on a light or dark canvas.
- Pick a tag colour next to one already in use (teal beside green). Each meaning gets its own clearly different hue.
- Box UI copy, or use more than one purple chip style for codes.
- Make notes panels or tables pure white. Notes sit on `#E3E3E3`, tables and strips on `#F1F1F4`.
- Use a light card tab, or leave out the header rule. Tabs are ink with white text, meta `#6B6B6B`, rule 2px ink.
- Use two feature colours inside one feature, or the same colour for two features side by side.
- Put a Scope tag inside a notes panel (`Scope · Changed`). It goes in the card's header row.
- Make the item name the big board title, or write titles and sub-headings in mono caps. The title is the page type in sentence case; the item lives in the location pill.
- Change the accent color inside boards to match the section hue.
- Use shadows, gradients, or radius > 16 (boards 16, line bars and cards 12).
- Mix strokes, heavy rules, and cards. Use the divider grid.
