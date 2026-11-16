# TDC Office — shared design

Every TDC Office page looks like one family: the Dental Dashboard, every module (Daysheet, Treatment Finder, Voice Charting, Practice Numbers, anything new), and the three standalone apps (Lab Tracker, Supply Room, Maintenance Tracker). This file is the rule book. Ryan approved this design on 2026-11-16.

**Who builds what**
- The **Dental Dashboard** owns the shared pieces: `/shared/tdc.css`, `/shared/shell.js`, the fonts and the logos. It turns this file into CSS variables and classes.
- **Modules** use those classes and variables. They don't ship their own fonts, colours or header.
- The **standalone apps** (Lab Tracker, Supply Room, Maintenance Tracker) are separate PHP apps with their own CSS. They already follow this design and are the **reference implementations**: when this file and their look disagree, ask Ryan which one is right, and update the other.
- The **Shared Design** agent keeps this file, `brand/` and the reference pictures up to date. Design changes go through it (or Ryan), not through individual modules.

Pictures of the finished design are in [`brand/reference/`](brand/reference/). Look at them before building a page.

## 1. The idea in one line

One warm, quiet base taken from the Toledo Dental Care logo, the same building blocks everywhere, and **one accent colour per app** so you always know where you are.

## 2. Colours

### Base (the same in every app)

| Token | Light | Dark | Use |
|---|---|---|---|
| `--tdc-ink` | `#1F2D33` | `#E5EBE7` | All text |
| `--tdc-navy` | `#23333D` | `#93B4FF` | Logo navy. Dashboard accent, header text |
| `--tdc-olive` | `#51573D` | `#A8B680` | Logo green. Brand colour, Lab Tracker accent |
| `--tdc-bg` | `#F4F4EF` | `#11191C` | Page background (warm off-white) |
| `--tdc-surface` | `#FFFFFF` | `#18242A` | Cards, rows, header |
| `--tdc-sunk` | `#EBECE4` | `#1F2E34` | Inputs, group headings, empty track of a progress bar |
| `--tdc-line` | `#D7D9CD` | `#2D3E45` | Borders and dividers |
| `--tdc-muted` | `#5C6459` | `#9AA69F` | Secondary text (still at least 4.5:1 on `--tdc-bg`) |

### Status (the same meaning in every app)

| Token | Light text / soft background | Means |
|---|---|---|
| `--tdc-alert` / `--tdc-alert-soft` | `#B3261E` / `#F9DEDB` | 911, overdue, late, out of stock |
| `--tdc-911` | `#C62828` (white text) | The solid **911** chip only |
| `--tdc-warn` / `--tdc-warn-soft` | `#8A4B00` / `#FBEAD2` | Due soon, on hold, needs a date, expiring |
| `--tdc-low` | `#F2C12E` | Stripe and bar fill for "below reserve" (never text) |
| `--tdc-ok` / `--tdc-ok-soft` | `#2D7A3A` / `#DCEFDF` | Done, ready, stocked, completed |
| `--tdc-new` / `--tdc-new-soft` | `#56696E` / `#E9EEEF` | Not started, never logged |
| `--tdc-info` / `--tdc-info-soft` | `#2F557F` / `#E0E8F2` | Sent out, on the way, informational |

Never use colour alone: every status also has a word or an icon (the "911" chip, "Overdue", a check mark).

### One accent per app

The accent colours the strip under the header, the active status tile, the page banner, the main "+ New" button and focus rings. **It is the only colour that changes between apps.** A module sets it once: `<body data-module="daysheet">` and `tdc.css` picks the accent.

| App | Accent | Soft | Notes |
|---|---|---|---|
| Dental Dashboard | `#23333D` | `#E1E6EA` | Logo navy |
| Maintenance Tracker | `#0E6B73` | `#DCEFEF` | Teal |
| Lab Tracker | `#4E5833` | `#E5E9D8` | Olive |
| Supply Room | `#1C3466` | `#DFE6F4` | Navy |
| Dental Daysheet | `#2B5FAE` | `#E0E9F7` | Blue |
| Treatment Finder | `#6A3FC4` | `#EFE8FC` | Violet |
| Voice Charting | `#A02A72` | `#F9E3F0` | Magenta |
| Practice Numbers | `#7A4E1C` | `#F5E9DA` | Bronze |

A new module gets a new accent here before it ships (ask the Shared Design agent). White text on every accent is at least 4.5:1.

### Header buttons: colour by what they do

Header buttons are tinted (soft background, coloured border and text). The **same kind of button has the same colour in every app**:

| Kind | Colour | Examples |
|---|---|---|
| Lists / browse | Navy `#1C3466` | All tasks, All cases, Saved lists |
| New | The app's accent | + New task, + New case, + New product |
| Log / history | Olive `#4E5833` | Log, History, My dictations |
| Setup / settings | Grey `#46575B` | Setup, Settings |
| Money | Green `#1E7A3C` | Spending, Production |
| Warning | Amber `#8A4B00` | Expiring (with a count) |
| Tool | Cyan `#0891B2` | Editor |

The current page's button is filled solid in its colour with white text.

## 3. Fonts

| Font | Use | Weights |
|---|---|---|
| **Archivo** (narrow: `font-stretch: 80%`) | App name, page banner title, section headings, group headings in capitals | 700–800 |
| **Figtree** | Everything else: text, buttons, labels | 400–800 |
| **JetBrains Mono** | Dates, times, counts, money, tooth numbers, chart numbers, version numbers | 400–700 |

The font files are in [`brand/fonts/`](brand/fonts/) (free licence, SIL OFL). The Dashboard serves them from `/shared/fonts/`. **Never load fonts from Google or any other outside site**: the live office site has no outside access, and patient pages shouldn't call out.

Sizes: app name 28px desktop / 20–22px phone; page banner title 24px / 18px; body 15–16px; small labels at least 12px.

## 4. The logo

| File (in `brand/`) | Where |
|---|---|
| `logo-horizontal.png` | Desktop and tablet header: circle, trees, tooth **and** the words TOLEDO DENTAL CARE |
| `logo-mark.png` | Phone header: the circle, trees and tooth only, no words |
| `logo-horizontal-dark.png`, `logo-mark-dark.png` | The same, for dark mode (light navy parts, lighter olive) |
| `stacked2.png` | Login page and Dashboard home |

Never stretch, squash or crop the logo. Set its height and let the width follow (`width: auto`). The mark must show the whole circle.

## 5. Page layout

- **Width:** content is at most **1140px** wide, centred, with 16px side padding. Same for header and page.
- **Corners:** 10px on cards, tiles, buttons and inputs. Pill buttons use full rounding.
- **Touch:** everything you can tap is at least 44px tall on tablets and phones (36px header pills are fine on desktop only).
- **Sizes to check:** 1400px, 1100px, 1000px, 768px (tablet) and 390px (phone).

### Header (drawn by `shell.js`)

Desktop and wide tablet (1100px and up): a white bar with a **4px strip in the app's accent** along the bottom, laid out in three columns:

```
| logo (240px column) | App name (Archivo 28px)         | user · Lock · Log out      |
|                     | Monday, November 16, 2026 (mono) | [List] [+ New] [Log] [Setup] |
```

- The **app name always starts in the same place**, right after the 240px logo column, in every app. Don't centre it.
- The right column takes as much room as the buttons need. Row 1 is the person (user name or "Logging as", sound, theme). Row 2 is the page's own buttons, colour-coded as in §2.
- Between 641px and 1099px the same parts are kept, but the logo column shrinks to the logo's own width and the buttons may wrap to a second line.
- Switching apps: an **Apps** button (grid icon) in row 1 opens the list of modules and links (Home, Daysheet, Lab Tracker and so on). In the suite, "Logging as" is replaced by the logged-in user's name, Lock and Log out.

Phone (640px and down):

```
| mark | App name (Archivo 20–22px, cut with … if needed) | initials ▾ |
| page banner (one slim line)                                         |
| search box ............................................ | Filters |
```

Everything else from the header (Apps, Log, Setup, sound, theme, Log out) moves into the **More** tab of the bottom bar.

### Status tiles (top of a list page)

A row of 4–6 tiles, one per status of the thing the page lists (Today / Overdue / Due soon …, or Active / Sent out / Overdue …).

- Each tile shows **count · icon · label on one line**, 48px tall, 2px border in its status colour, soft background.
- The selected tile is filled solid in its colour with white text, with a dark outline ring.
- A small **corner badge** (red, mono) can flag urgent items inside a tile, for example `2 911`. Use an icon or text, never emoji.
- Phone: the tiles become the **bottom tab bar** (icon over a short label, count as a badge) plus a **More** tab at the end.

### Page banner

Under the tiles: a soft band in the accent colour with a 6px accent edge on the left. Icon and title (Archivo 24px) on the left, a mono count on the right ("18 cases", "24 need attention"). One optional line of help text under it.

### Search and filters

Under the banner: a search box that fills the row (soft `--tdc-sunk` background, magnifier icon), followed by 1–3 dropdown filters ("All labs", "All frequencies"). Phone: the search box plus a **Filters** button that opens the dropdowns.

### Group by

A segmented control labelled **Group by** above the list (for example Due date · Case type · Lab). The choice is remembered on the device. Phone: hide the "Group by" label and keep the control.

### Rows (lists of tasks, cases, products, patients)

- White rows inside a card with a 1px border, separated by 1px lines. Group headings are `--tdc-sunk` bars with an Archivo capital name and a count on the right.
- A **6px solid stripe** on the left edge in the row's status colour (red 911/overdue, amber due soon, yellow below reserve, green done/ready, grey not started). Solid, never hatched or tinted across the whole row.
- Name in Figtree 600; under it, small **chips** (6px corners: type, tooth, lab, frequency). The solid red **911** chip marks the most urgent rows.
- Dates and money on the right in mono, the most urgent line in red.
- A **progress bar** wherever there are steps or stock: 6px tall, rounded, `--tdc-sunk` track, filled in the status colour, with a mono label ("2/6 steps", "1 of 3 · 911 at 1"). An optional dark tick marks a threshold (the 911 level).
- The main action on a row (Done, Order quantity) sits at the far right.

### Cards, dashboard tiles and links

Keep the existing `.tdc-card`, `.tdc-tile`, `.tdc-table`, `.tdc-form` and `.tdc-badge-*` classes, restyled with the tokens above. Dashboard tiles get a 5px accent strip on top in the colour of the module they come from.

### Empty, loading and error states

- Empty: a dashed-border card with a bold first line and one sentence ("All caught up", "No tasks match … Clear search").
- First-time "Who's logging?" (standalone apps): the same dashed card, shown until initials are picked.
- A module that doesn't answer: its tile says "Not responding" on a dashed card; the rest of the page keeps working.

## 6. Dark mode

Every page follows the device setting and has a theme button. Use the dark column of §2, the `-dark` logo files, and keep the same accents (lighten an accent only if it falls below 4.5:1 on `--tdc-surface`).

## 7. Words

- Same labels everywhere: **Logging as**, **Group by**, **Setup**, **Log**, **More**, **+ New …**, **Clear search**, **Not responding**.
- Sentence case for buttons and labels; capitals only for group headings.
- Dates as `Mon, Nov 16` in rows and `Monday, November 16, 2026` in the header.

## 8. Classes the Dashboard adds to `tdc.css`

Modules use these instead of writing their own:

| Class | What |
|---|---|
| `.tdc-stats` / `.tdc-stat[data-status]` (+ `aria-selected`) | Status tile row and tiles (become the phone tab bar) |
| `.tdc-pagehead` | Page banner (`h2`, `.tdc-count`, optional `p`) |
| `.tdc-find` | Search box + filters row |
| `.tdc-seg` | Segmented control (Group by), buttons with `aria-pressed` |
| `.tdc-list`, `.tdc-group`, `.tdc-row[data-status]` | List card, group heading, row with status stripe |
| `.tdc-chip`, `.tdc-chip-911` | Small labels |
| `.tdc-meter` (`--value`, optional `--mark`) | Progress / stock bar |
| `.tdc-hbtn[data-kind="list|new|log|setup|money|warn|tool"]` | Colour-coded header buttons. A module puts them in `<nav class="tdc-actions">` and `shell.js` moves them into the header (and into More on phones) |

## 9. Checklist before a PR

- [ ] Looks like the pictures in `brand/reference/` at 1400px, 768px and 390px.
- [ ] Only `--tdc-*` colours and `.tdc-*` classes; no new fonts, no outside font links.
- [ ] Status colours mean what §2 says, and each has a word or icon too.
- [ ] App name in the shared spot; accent set by `data-module`.
- [ ] Touch targets at least 44px on tablet and phone; text at least 4.5:1.
- [ ] Works in dark mode.

## Changes

- 2026-11-16: first version, from the shared look Ryan approved for Lab Tracker, Supply Room and Maintenance Tracker.
