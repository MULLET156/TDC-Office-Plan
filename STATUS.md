# Status

Each agent updates **only its own row**, in the same PR as its work. Ryan updates the "Merged" column.

| Module | Repo | Phase | Current work | Waiting on | Last merged version |
|---|---|---|---|---|---|
| Plan | TDC-Office-Plan | 0 | Plan merged | — | 2026-11-15 |
| Shared Design | TDC-Office-Plan (DESIGN.md, brand/) + Lab Tracker, Supply Room, Maintenance Tracker | — | DESIGN.md v1 merged. Lab Tracker, Supply Room and Maintenance Tracker all on the shared design (layout, header, colours, tiles, rows, search, group by, phone layout) with CLAUDE.md pointing their agents to DESIGN.md. | Dashboard to apply DESIGN.md to tdc.css and shell.js | 2026-11-16 |
| Connector and Gateway | opendental-claude | 1 | **v0.10.0 released**: gateway `"stage"` (design/live, unset = live) and stage-following `checkStage()`; Ryan's hygiene codes as the default (`hygieneCodes`, shared); demo gateway fixed for Hostinger (#5). Still on #6: tunnel guide, design-stage keys, trimming report columns | Ryan: tunnel choice (domain DNS on Cloudflare, a separate domain, or Tailscale Funnel) | 0.10.0 (main) |
| Dental Dashboard | TDC-Dental-Dashboard | 1 | Shared design merged and live (https://palevioletred-viper-775768.hostingersite.com): tdc.css and shell.js follow DESIGN.md incl. the page-colour header line (#22); sample module Design kit page (`/m/hello/kit`) for module agents to copy. Module agents can start. | Shared Design: confirm colour values in #18 | 0.1.0 |
| Lab Tracker (standalone) | TDC-Lab-Tracker | — | Live on Hostinger, in daily use. **Dashboard tiles live** (`/tdc/tiles/due-week`, `late`, `to-check`; counts, case type and date only), off until `TILE_TOKEN` is set. Office settings updated: Supply Room materials, Blue Sky Bio implant default, Dr. Millet default doctor. | Dashboard: standalone tiles with a token per app (#23); then Ryan sets `TILE_TOKEN` | 2026-11-16 |
| Dental Daysheet | TDC-Dental-Daysheet | 2 | Not started | Dashboard v0.1, reports | — |
| Treatment Finder | TDC-Treatment-Finder | 2 | Not started | Dashboard v0.1, reports | — |
| Voice Charting | TDC-Voice-Charting | 3 | Not started | Dashboard v0.1, gateway mode | — |
| Practice Numbers | TDC-Practice-Numbers | 4 | Not started | Dashboard v0.1 | — |
| Supply Room | TDC-Supply-Room (standalone PHP, Hostinger) | — | Live and in daily use. Proposed Dashboard tiles (To order, Waiting to arrive) in #23. | Ryan: OK on #23 | 2026-11-16 |
| Maintenance Tracker | TDC-Maintenance-Tracker (standalone PHP, Hostinger) | — | Live and in daily use. Merged today: shared design, Downtime list (no-due-date chores, suggested on Today when nothing is due), Edit tasks on the All tasks page, one-line page banner with an (i) for help text. Proposed Dashboard tiles (Overdue, Due this week) on #24. | Ryan: OK on #24 | 2026-11-16 |
| Installer | TDC-Office-Installer | 5 | Not started | All modules v0.1 | — |
