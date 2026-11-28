# TDC Office — the big plan

## Goal

One office website for Toledo Dental Care (Toledo, Oregon), running on the office server, that staff open from operatory PCs, front-desk PCs and Android tablets. It's made of separate **modules**, each in its own GitHub repo and built by its own agent, that plug into a shared **Dashboard**. Staff log in once and see one site.

The practice runs **Open Dental 26.2.40**. Every module reaches Open Dental only through the **gateway** in `MULLET156/opendental-claude`.

## The pieces

| Module | Repo | What it does | Open Dental access |
|---|---|---|---|
| Dental Dashboard | `MULLET156/TDC-Dental-Dashboard` | Front door: login, staff and roles, the shared header and look, home screen tiles, links to other apps. Passes requests to the modules. | **None.** It never calls Open Dental. |
| Dental Daysheet | `MULLET156/TDC-Dental-Daysheet` | Today's patients, with planned treatment that isn't scheduled, x-ray history and frequency limits, and lab cases due before their seat date. | Read only |
| Treatment Finder | `MULLET156/TDC-Treatment-Finder` | Filter patients to find work to schedule: planned treatment, no future appointment, insurance benefits remaining, x-rays due, and so on. Save or export lists. | Read only |
| Voice Charting | `MULLET156/TDC-Voice-Charting` | Dictate (with Wispr Flow) or type, then review a draft treatment plan or note, then Confirm to write it to Open Dental. | **Read and write** (chart only, never signs) |
| Practice Numbers | `MULLET156/TDC-Practice-Numbers` | Production, collections, A/R, new patients, trends. Owner and admin only. | Read only |
| Connector and Gateway | `MULLET156/opendental-claude` (exists) | Shared Open Dental code (`core/`), the gateway, and **all reports**. | Owns it |
| Shared Design | TDC-Office-Plan (DESIGN.md, `brand/`) | Keeps every page in one look: DESIGN.md, logos, fonts, reference pictures; keeps Lab Tracker, Supply Room and Maintenance Tracker in line; reviews the Dashboard's shared look and module pages. | None |
| Office setup (later) | `MULLET156/TDC-Office-Installer` | One TrueNAS setup (app or compose file) that runs the gateway, the Dashboard and every module together, with keys, secrets and updates. | — |

Apps that already exist stay where they are. The Dashboard only links to them:
- TDC Lab Tracker (Hostinger)
- TDC Supply Room
- TDC Maintenance Tracker (Hostinger)

## Where it runs (Ryan, 2026-11-27)

There is **no demo database**: the "demo" Open Dental turned out to be the office's real Open Dental. So the suite uses the **real database, with full patient details**, and everything that touches it runs **inside the office**.

| | Building and testing | Office (in use) |
|---|---|---|
| Where | The agent's own machine | The office **TrueNAS** box: the Dental Dashboard, every module and the gateway, each as a container that updates itself from GitHub (like the gateway, opendental-claude docs/truenas.md) |
| Data | **Fake data only**: the local demo gateway (`npm run dev` in opendental-claude) and the module's own test fakes | Ryan's **real Open Dental** through the gateway (`"stage": "live"`, on 127.0.0.1:8787), one key per module |
| Who uses it | Agents and automated tests | Staff on the office network. Later, from outside: see "Outside use" below |
| Deploys | — | Merging to `main` builds the module's image; TrueNAS pulls the new version |

**Outside use (later):** a Cloudflare Tunnel to the **Dental Dashboard only**, never to the gateway or a module port. It sits behind a **Cloudflare Access** sign-in (an emailed code to approved addresses), then the Dashboard's own login. This needs toledodentalcare.com's DNS moved to Cloudflare.

**Hostinger** keeps only the standalone apps (Supply Room, Maintenance Tracker, and the Lab Tracker until it converts). No Open Dental module runs there. The Hostinger copy of the Dashboard is a preview with no Open Dental data; it's retired once the office Dashboard is running.

**Keep patient data where it belongs:**
- The gateway sends each module **only what that module needs**: its own key, its own allow list, and reports that return only the columns a page shows.
- Modules keep Open Dental data in memory only, briefly. `data/` holds only the module's own records (saved lists as PatNums plus a call status, drafts while they wait for Confirm).
- No patient data in logs, code, tests, issues, PRs or screenshots, ever.
- **Agents never pull real patient details into their chat.** Build and test on fake data. When something must be checked against the office system, use test patient **PatNum 8133** (Ryan A. Millet), or totals and counts, or ask Ryan to look.
- **Writes** (Voice Charting only) always go through a review screen and a **Confirm** tap, then a read-back. Nothing is ever signed. Live write tests go to PatNum 8133 only.
- Claude and Wispr Flow see real dictations only once Ryan has the Anthropic and Wispr Flow BAAs in place.

## Standalone apps

The **Lab Tracker**, **Supply Room** and **Maintenance Tracker** are working PHP apps on Hostinger, already on the shared design (DESIGN.md). They are **not** converted into Node.js modules now. Each has its own agent (`prompts/9`, `10`, `11`).
- **Now:** the Dashboard links to them, and each serves a couple of Dashboard tiles (once Ryan approves that contract change).
- **Supply Room and Maintenance Tracker** hold no patient data, so they can stay on Hostinger for good. Converting them is optional, for example later for one login across the suite.
- **Lab Tracker** holds real patient information, so it is the **first one to convert**: a module on the office server, reading lab cases from Open Dental. That happens after the Daysheet and Treatment Finder modules work, and before the suite goes live. Until then it adds no new patient fields and shows the minimum.

## How it fits together (live stage)

```
 office PCs and tablets
        │  https://office.toledodentalcare.com  (just a link and a password)
        ▼
 ┌─────────────────────────┐
 │ Dashboard  :443         │  login, roles, header, tiles
 └──┬──────┬──────┬──────┬─┘  passes /m/<module>/... to modules (signed, see CONTRACT.md)
    ▼      ▼      ▼      ▼
 Daysheet Finder Voice  Numbers     each on 127.0.0.1 only
  :3101   :3102  :3103   :3104
    └──────┴──────┴──────┘
        │  own Bearer key per module
        ▼
 Gateway 127.0.0.1:8787 ──► Open Dental API Service :30223
```

All of it runs on the office server. **No patient data goes on Hostinger or on any host without a BAA.**

## Rules for every agent

1. **Read this repo first**, then your own repo's `CLAUDE.md`. Follow [CONTRACT.md](CONTRACT.md) exactly. If it needs to change, open an issue in this repo instead of working around it.
2. **Merge as you go** (Ryan, 2026-11-16): work on a short branch, open a pull request, and **merge it yourself** as soon as CI is green and your own checks pass. Don't wait for Ryan. Merge small and often. Still: contract changes need Ryan's OK first; changes that write to Open Dental are tested on PatNum 8133 before merging; and in a repo that deploys a live site on merge, check the live site right after and revert the PR at once if something broke.
3. **Before changing anything**, pull the latest `main` and check open PRs, so newer work isn't overwritten.
4. **Zero dependencies:** Node.js 20+ built-in modules only. External programs such as the Whisper speech engine are allowed, but they're installed by the installer, not through npm.
5. **No patient data** in code, tests, issues, PRs, logs, screenshots or chat. Build and test on fake data. Checks against the office system use test patient **Ryan A. Millet, PatNum 8133**, or totals only. **Writes** go to PatNum 8133 only.
6. **Never ask for, print or commit secrets** (Open Dental keys, gateway keys, the Anthropic key, module secrets). They live in a `.env` file that's never committed, and later in Windows DPAPI storage set up by the installer.
7. **Open Dental only through the gateway**, each module with its own gateway key. Use the shared `core/` code. Don't write your own Open Dental calls.
8. **Only the Connector agent edits `opendental-claude`.** Need a new report? Open an issue there titled `Report request: <name>` that describes the inputs and outputs. Meanwhile, build against fake data shaped like that report.
9. **Writes:** only Voice Charting writes, only after a staff member taps Confirm, and every write is read back. **Never sign notes or plans.**
10. **Write for Ryan:** plain language, step-by-step setup, Windows first.
11. When you finish a piece of work, update **your own row** in [STATUS.md](STATUS.md) in the same PR.

## Build order

| Phase | What | Agents running |
|---|---|---|
| 0 | Ryan creates the repos and merges this plan | — |
| 1 | **Dashboard:** login, roles, header, tiles, signed pass-through, plus a built-in "hello" sample module. **Connector:** gateway mode for `core/`, a fake-data dev gateway, and the first reports. | Dashboard, Connector |
| 2 | **Daysheet**, then **Treatment Finder** (they share reports) | Daysheet, Finder, Connector |
| 3 | **Voice Charting**, with dictation by Wispr Flow (local Whisper later, if needed) | Voice, Connector |
| 4 | **Practice Numbers** | Numbers |
| 5 | **Office setup**: one TrueNAS setup for the gateway, Dashboard and modules, with keys, certificates and updates | Installer |

Agents merge their own PRs, so several can run at once. Ryan follows along in STATUS.md and the merged PRs.

## Decisions already made

- Everything that touches Open Dental runs in the office on TrueNAS, with the real database; building and testing use fake data (see "Where it runs").
- **Fully web-based for staff: nothing is installed on any office PC or tablet.** Staff open a link (for example `https://office.toledodentalcare.com`) in Chrome or Edge and log in with their password. No certificate to trust, no per-device setup.
- The only install is on the **office server** (the TrueNAS box).
- Hosted in the office, on the same server as the gateway. HTTPS is required (browsers only allow the microphone on secure pages). It uses a **real, publicly trusted certificate** (Let's Encrypt, free) for a name under the practice's own domain, proven by DNS, so the server never has to be open to the internet. The name points to the server's office-network address, so it only works inside the office.
- **Speech-to-text: Wispr Flow** (Ryan, 2026-11-19). Staff dictate with the Wispr Flow app into Voice Charting's text box. The practice signs Wispr Flow's HIPAA BAA, which locks zero data retention on: individuals sign in the desktop or iOS app, and Android tablets need an organization (team) account with the admin-signed BAA. Flow is optional on any device (typing works too). A built-in mic with local Whisper on the office server is a later option. **Never** the browser's built-in Web Speech API (Chrome sends audio to Google with no BAA).
- Claude is called through the Anthropic API with Ryan's own key, with plain `fetch` and no SDK. Ryan must confirm a BAA with Anthropic before the app is used on real patients.
- Staff log in individually, so the audit trail shows who dictated and who confirmed each change.
- Devices: Windows PCs in operatories, front-desk PCs, Android tablets.
- "Hygiene" treatment is left out of the "planned but not scheduled" lists. Hygiene codes (Ryan, 2026-11-16): **D0120, D0140, D0150, D0210, D0272, D0274, D1110, D1120, D1206, D1208, D4341, D4342, D4346, D4355, D4910**. Keep the list in one setting, not hard-coded per module.
- "X-ray eligibility" means the insurance plan's frequency limits plus the dates x-rays were last taken. A live eligibility check with the insurer through a clearinghouse is out of scope for now.

## Open questions (Ryan)

- [ ] Exact list of procedure codes that count as "hygiene".
- [ ] Which roles see which modules (draft in SITE-OUTLINE.md).
- [ ] Anthropic BAA confirmed.
