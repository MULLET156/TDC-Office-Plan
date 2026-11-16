# Status

Each agent updates **only its own row**, in the same PR as its work. Ryan updates the "Merged" column.

| Module | Repo | Phase | Current work | Waiting on | Last merged version |
|---|---|---|---|---|---|
| Plan | TDC-Office-Plan | 0 | Plan merged | — | 2026-11-15 |
| Shared Design | TDC-Office-Plan (DESIGN.md, brand/) + Lab Tracker, Supply Room, Maintenance Tracker | — | DESIGN.md v1, logos (desktop, phone mark, dark), fonts, reference pictures. Lab Tracker, Supply Room and Maintenance Tracker updated to the shared layout (header, width, tiles, rows, search, group by). | Dashboard to apply DESIGN.md to tdc.css and shell.js; swap the standalone apps to the shared base colours and TDC logo (planned after clinic hours) | — |
| Connector and Gateway | opendental-claude | 1 | **v0.9.0 released** (incl. PR #3 lab sync PatNum option): gateway mode, demo gateway (fake data; `npm run dev` / `start-demo.cmd`), daysheet / unscheduled_treatment / no_future_appointment / xray_history / insurance_remaining / unsigned_notes reports, per-module keys, sync-core (`node scripts/sync-core.js 0.9.0`) | Demo gateway on Hostinger (subdomain from Ryan); hygiene code list (Ryan) | 0.9.0 (main) |
| Dental Dashboard | TDC-Dental-Dashboard | 1 | v0.1 merged: login, roles, settings, tiles, signed pass-through, shared look, hello sample module, contract checker (`scripts/check-module.js`), Let's Encrypt DNS-01 client. Module agents can start. | Contract questions #6, #7, #8. Design-stage site: https://palevioletred-viper-775768.hostingersite.com (auto-deploys from main) | 0.1.0 |
| Dental Daysheet | TDC-Dental-Daysheet | 2 | Not started | Dashboard v0.1, reports | — |
| Treatment Finder | TDC-Treatment-Finder | 2 | Not started | Dashboard v0.1, reports | — |
| Voice Charting | TDC-Voice-Charting | 3 | Not started | Dashboard v0.1, gateway mode | — |
| Practice Numbers | TDC-Practice-Numbers | 4 | Not started | Dashboard v0.1 | — |
| Installer | TDC-Office-Installer | 5 | Not started | All modules v0.1 | — |
