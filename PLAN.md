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
| Voice Charting | `MULLET156/TDC-Voice-Charting` | Speak, then review a draft treatment plan or note, then Confirm to write it to Open Dental. Includes the speech-to-text service. | **Read and write** (chart only, never signs) |
| Practice Numbers | `MULLET156/TDC-Practice-Numbers` | Production, collections, A/R, new patients, trends. Owner and admin only. | Read only |
| Connector and Gateway | `MULLET156/opendental-claude` (exists) | Shared Open Dental code (`core/`), the gateway, and **all reports**. | Owns it |
| Installer (later) | `MULLET156/TDC-Office-Installer` | One Windows `Setup.exe` that installs the gateway, the dashboard and the modules as Windows services. | — |

Apps that already exist stay where they are. The Dashboard only links to them:
- TDC Lab Tracker (Hostinger)
- TDC Supply Room
- TDC Maintenance Tracker (Hostinger)

## Two stages

| | **Design stage** (now) | **Live stage** (later) |
|---|---|---|
| Where | Ryan's **Hostinger** account (Node.js apps, one subdomain per module) | **Office server**, with the module ports below |
| Data | **Fake data only**, from the Connector's *demo gateway* (fake Open Dental) on Hostinger | Real Open Dental through the real gateway |
| Who uses it | Ryan and staff trying out designs | Staff, on the office network |
| Deploys | Merging to `main` auto-deploys that repo to Hostinger | Installer / updater on the office server |

**The design stage must never touch real patient data.** Every module reads `TDC_STAGE` (`design` or `live`). In `design`, it checks at startup that the gateway's `/health` reports `"fake": true`, and refuses to start if not. Nothing on Hostinger is ever given the real gateway's address or keys.

The same code runs in both stages. Only the settings change (see CONTRACT.md §1). Moving to live means installing the same repos on the office server. The Dental Dashboard and Practice Numbers (totals only) could stay on Hostinger later, but that's decided when we go live.

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
  :3101   :3102  :3103   :3104      (Voice also uses Speech :3110)
    └──────┴──────┴──────┘
        │  own Bearer key per module
        ▼
 Gateway 127.0.0.1:8787 ──► Open Dental API Service :30223
```

All of it runs on the office server. **No patient data goes on Hostinger or on any host without a BAA.**

## Rules for every agent

1. **Read this repo first**, then your own repo's `CLAUDE.md`. Follow [CONTRACT.md](CONTRACT.md) exactly. If it needs to change, open an issue in this repo instead of working around it.
2. **Never push to `main`.** Work on a branch and open a pull request. Ryan reviews and merges.
3. **Before changing anything**, pull the latest `main` and check open PRs, so newer work isn't overwritten.
4. **Zero dependencies:** Node.js 20+ built-in modules only. External programs such as the Whisper speech engine are allowed, but they're installed by the installer, not through npm.
5. **No real patient data** anywhere: code, tests, issues, logs or screenshots. Use fake data. For live testing, use only test patient **Ryan A. Millet, PatNum 8133**.
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
| 3 | **Voice Charting** with local Whisper (cloud speech-to-text as an option, off until there's a BAA) | Voice, Connector |
| 4 | **Practice Numbers** | Numbers |
| 5 | **Installer**: one `Setup.exe` with Windows services, automatic certificate renewal and key storage | Installer |

Keep **2 or 3 agents running at a time**. Each PR waits for Ryan's review, so more agents than that means a growing review queue.

## Decisions already made

- Design stage on Hostinger with fake data; live stage on the office server (see "Two stages").
- **Fully web-based for staff: nothing is installed on any office PC or tablet.** Staff open a link (for example `https://office.toledodentalcare.com`) in Chrome or Edge and log in with their password. No certificate to trust, no per-device setup.
- The only install is on the **office server**, done once by the Installer.
- Hosted in the office, on the same server as the gateway. HTTPS is required (browsers only allow the microphone on secure pages). It uses a **real, publicly trusted certificate** (Let's Encrypt, free) for a name under the practice's own domain, proven by DNS, so the server never has to be open to the internet. The name points to the server's office-network address, so it only works inside the office.
- Speech-to-text: build a switch with two options. **Local Whisper** is built first. A **cloud service under a BAA** (AWS Transcribe Medical or Azure) is off until there's a BAA. **Never** use the browser's built-in Web Speech API.
- Claude is called through the Anthropic API with Ryan's own key, with plain `fetch` and no SDK. Ryan must confirm a BAA with Anthropic before the app is used on real patients.
- Staff log in individually, so the audit trail shows who dictated and who confirmed each change.
- Devices: Windows PCs in operatories, front-desk PCs, Android tablets.
- "Hygiene" treatment is left out of the "planned but not scheduled" lists.
- "X-ray eligibility" means the insurance plan's frequency limits plus the dates x-rays were last taken. A live eligibility check with the insurer through a clearinghouse is out of scope for now.

## Open questions (Ryan)

- [ ] Exact list of procedure codes that count as "hygiene".
- [ ] Which roles see which modules (draft in SITE-OUTLINE.md).
- [ ] Office server specs (CPU, RAM, GPU), to choose the Whisper model size.
- [ ] Anthropic BAA confirmed.
