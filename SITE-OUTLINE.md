# Site outline

Address: `https://office.toledodentalcare.com` (final name to confirm). Staff need only this link and their password; nothing is installed on their devices. Every page has the shared header: **TDC logo · Home · module links (by role) · user name · Log out**.

```
https://tdc-office/
├── /login                         Dashboard
├── /                              Dashboard home (tiles + app links)
├── /settings                      Dashboard: staff, roles, links, tiles      [owner, admin]
│
├── /m/daysheet/                   Daysheet
│   ├── /                          Today
│   ├── /day?date=                 Any day
│   └── /unscheduled               All patients with planned, unscheduled treatment
│
├── /m/finder/                     Treatment Finder
│   ├── /                          Filter builder + results
│   ├── /lists                     Saved lists
│   └── /lists/<id>                One saved list (call status, export CSV)
│
├── /m/voice/                      Voice Charting                [doctor, hygienist, assistant, owner]
│   ├── /                          Pick patient → choose Plan or Note → dictate (Wispr Flow) or type
│   ├── /draft/<id>                Review draft → edit → Confirm / Discard
│   ├── /history                   My recent dictations and what was written
│   └── /settings                  Claude model, dental word list             [owner, admin]
│
├── /m/numbers/                    Practice Numbers              [owner, admin]
│   ├── /                          This month at a glance
│   ├── /production                By provider / day / code
│   ├── /collections               Patient + insurance
│   └── /ar                        A/R aging, outstanding claims
│
└── links (open in a new tab):  Lab Tracker · Supply Room · Maintenance Tracker
```

## Dashboard home

- **Top:** "Good morning, <name>" · today's date · shortcut buttons for the user's most-used modules.
- **Tiles grid** (each from a module, filtered by role):
  - Daysheet: *Today's schedule*, *Lab cases due before seat*
  - Treatment Finder: *Unscheduled treatment ($ total)*
  - Voice Charting: *My drafts waiting*, *Unsigned notes*
  - Practice Numbers: *Production MTD* (owner and admin only)
- **Other apps:** link cards for Lab Tracker, Supply Room and Maintenance Tracker.
- If a module is down, its tiles show "Not responding" and the rest of the page still works.

## Daysheet: Today

The table lists one row per appointment: time · patient · provider/op · appointment procedures · badges. Tapping a row opens a side panel with:
- **Planned, not scheduled:** procedure, tooth, fee, priority (hygiene excluded).
- **X-rays:** BWX / FMX / Pano, each showing the last date taken, the plan's limit (for example "1 per 12 mo") and the date it's next covered. Overdue, due today and covered today are colour-coded.
- **Lab cases** tied to the appointment, and their status.

The **Unscheduled** page lists patients, the planned $ total and the oldest plan date, and links to the Treatment Finder.

## Treatment Finder

- **Filters** (combine any): has planned treatment (code or category, min $) · no future appointment · last visit before/after · insurance plan / carrier · benefits remaining ≥ $ · x-rays due · recall overdue · provider · age range.
- **Results table:** patient · planned $ · benefits remaining · last visit · next appointment · phone shown only after a tap. Sortable.
- **Save as list:** mark each patient as called / scheduled / declined. Export to CSV.

## Voice Charting

1. Pick a patient (search) and choose **Treatment plan** or **Note** (progress or group note).
2. Dictate into the large text box with **Wispr Flow** (or type), then tap **Draft**. Claude turns the dictation into a draft.
3. **Draft** screen:
   - For a plan: each line shows code, tooth/surfaces, priority/phase and fee estimate, and can be edited or removed.
   - For a note: the note text can be edited.
4. **Confirm** writes to Open Dental and shows the read-back result. **Discard** throws the draft away. Nothing is signed.

## Practice Numbers

Month-to-date and year-to-date cards, a trend chart by month, and breakdowns by provider. The date range can be picked on every page.

## Roles (draft — Ryan to confirm)

| Module | owner | admin | doctor | hygienist | assistant | front |
|---|:-:|:-:|:-:|:-:|:-:|:-:|
| Dashboard | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Daysheet | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Treatment Finder | ✓ | ✓ | ✓ | ✓ |   | ✓ |
| Voice Charting | ✓ |   | ✓ | ✓ | ✓ (draft only) |   |
| Practice Numbers | ✓ | ✓ |   |   |   |   |
| Settings | ✓ | ✓ |   |   |   |   |
