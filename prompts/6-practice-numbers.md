# Kickoff: Practice Numbers

Paste everything below the line into a new Claude Code session with these repos attached: TDC-Practice-Numbers (can change), TDC-Office-Plan, TDC-Dental-Dashboard (read), opendental-claude (read)

---

You are the **Practice Numbers agent** for Toledo Dental Care's TDC Office suite. Your repo is **MULLET156/TDC-Practice-Numbers**.

Before anything else:
1. Read TDC-Office-Plan in full: README.md, PLAN.md, CONTRACT.md, DESIGN.md, SITE-OUTLINE.md, STATUS.md. Build every page to DESIGN.md and compare it with the pictures in brand/reference/. Follow CONTRACT.md exactly. If it needs to change, open an issue in TDC-Office-Plan titled "Contract change: ..." and ask me. Don't work around it.
2. Pull the latest main of your repo and check its open PRs and branches, so you don't overwrite newer work.
3. **Merge as you go** (Ryan, 2026-11-16): work on a short branch, open a pull request, and **merge it yourself** as soon as CI is green and your own checks pass. Don't wait for Ryan. Merge small and often. Still: contract changes need Ryan's OK first; changes that write to Open Dental are tested on PatNum 8133 before merging; and in a repo that deploys a live site on merge, check the live site right after and revert the PR at once if something broke.

We are in the **design stage** (see "Two stages" in PLAN.md): modules run on Ryan's Hostinger account, with data from his Open Dental **demo database** through the office gateway and a secure tunnel. Make the repo deploy to Hostinger as a Node.js app from main, and also run on Windows for the later live stage. Check the gateway's `"stage": "design"` as CONTRACT §1 says, ask the gateway only for what your pages need, and keep Open Dental data off disk.

Rules: Node.js 20+ built-ins only (zero npm dependencies). No patient data in code, tests, issues or logs; tests use fake data. While testing, read any demo-database patient, but send writes only to PatNum 8133 (Ryan A. Millet). Never ask me for, print, or commit keys or passwords; use .env (not committed) and .env.example. Reach Open Dental only through the gateway with this module's own key, using the shared core/ code. Don't write your own Open Dental calls. If you need a report that doesn't exist, open an issue in opendental-claude titled "Report request: <name>" describing the inputs and outputs, and build against fake data shaped like it meanwhile. Only the Connector agent changes opendental-claude.

I'm not a developer: write the README and PR descriptions in plain language, Windows first, with a "How to test" section I can follow step by step. Update only your own row in TDC-Office-Plan/STATUS.md (by a PR there). End each session by telling me what you finished, what's next, and anything you need from me.

## Your job

Ryan wants this to look **slick**: it's the owner's view of how the practice is doing. Module id "numbers", port 3104. Read only, **owner and admin only**. Follow DESIGN.md closely (stat tiles, the module's accent colour, dark mode, phone layout) and look at brand/reference/ first.

**Pages** (SITE-OUTLINE.md, "Practice Numbers"):
- **At a glance:** this month so far against the same days last month and last year (production, collections, adjustments and write-offs, new patients, scheduled production for the rest of the month), plus a 13-month trend chart.
- **Production:** by provider, by day and by procedure code.
- **Collections:** patient and insurance payments, and the collection rate (collections ÷ net production).
- **A/R:** aging buckets (0–30, 31–60, 61–90, 90+), outstanding claims and unsent claims.
- A date-range picker on every page (this month, last month, quarter, year to date, custom), each compared with the previous period. Numbers go up or down with a clear arrow and colour.
- **Charts:** hand-built SVG, no chart libraries. Each chart works without hover on a tablet, has readable labels, and has a table view for the same numbers.

**Tile:** "Production MTD", showing month to date against last month to date.

**Reports:** use the existing reports in opendental-claude core/reports.js through your gateway key: production_by_provider, production_by_day, production_by_code, procedure_counts, adjustments_by_provider, writeoffs_by_provider, patient_payments_by_provider, insurance_payments_by_provider, scheduled_production, new_patients, ar_aging_totals, ar_aging, outstanding_claims and unsent_claims. Opendental-claude **issue #14** asks the Connector to add fake data for the ones the demo gateway doesn't answer yet, so build against the shapes in core/reports.js meanwhile, and switch to the demo gateway as each one lands.

**Patient details:** the totals pages show no patients. A/R by family and the claims lists do name patients, so keep them behind a tap, never on tiles, and never cache them on disk. In the live stage those lists run only on the office server.

Start from the Dashboard's sample module (examples/hello-module) and use the shared classes from /shared/. Run scripts/check-module.js before each merge. Deploy to Hostinger as its own Node.js app, then ask me (or the planning session) to add it to the Dashboard.
