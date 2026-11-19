# Ryan's checklist: repos and agents

*Agents: this file is for Ryan. Your instructions are in CLAUDE.md and your kickoff message.*

## Step 1 — Create the repos on GitHub (once)

Owner **MULLET156**, with a README so the repo isn't empty. The repos are public, so they must never hold patient data or keys.

- [x] `TDC-Office-Plan` (plan committed 2026-11-15)
- [x] `TDC-Dental-Dashboard`
- [x] `TDC-Dental-Daysheet`
- [x] `TDC-Treatment-Finder`
- [x] `TDC-Voice-Charting`
- [x] `TDC-Practice-Numbers`
- [ ] `TDC-Office-Installer` (can wait until phase 5)

`opendental-claude` already exists.

## Step 1b — Hostinger (design stage)

- [ ] Check that your Hostinger plan can run **Node.js apps**. Business and Cloud plans can. Each module becomes its own app on its own subdomain; the agents will tell you the subdomain names when they're ready to deploy.
- [ ] Only fake data ever goes on Hostinger.

## Step 2 — Start the agents, in this order

Each agent is a **new Claude Code session**. In each session:
1. Attach the repos listed for that agent.
2. Paste in its kickoff message from `prompts/`.
3. Let it work. It ends by opening a pull request and telling you what to check.

| # | Agent | Start when | Repos to attach (✎ = it may change this repo) | Kickoff message |
|---|---|---|---|---|
| 1 | **Connector** | Now | opendental-claude ✎, TDC-Office-Plan | `prompts/1-connector.md` |
| 2 | **Dental Dashboard** | Now | TDC-Dental-Dashboard ✎, TDC-Office-Plan | `prompts/2-dashboard.md` |
| 3 | **Dental Daysheet** | Dashboard v0.1 merged | TDC-Dental-Daysheet ✎, TDC-Office-Plan, TDC-Dental-Dashboard, opendental-claude | `prompts/3-daysheet.md` |
| 4 | **Treatment Finder** | Dashboard v0.1 merged | TDC-Treatment-Finder ✎, TDC-Office-Plan, TDC-Dental-Dashboard, opendental-claude | `prompts/4-treatment-finder.md` |
| 5 | **Voice Charting** | Dashboard v0.1 merged and gateway mode released | TDC-Voice-Charting ✎, TDC-Office-Plan, TDC-Dental-Dashboard, opendental-claude | `prompts/5-voice-charting.md` |
| 6 | **Practice Numbers** | Dashboard v0.1 merged | TDC-Practice-Numbers ✎, TDC-Office-Plan, TDC-Dental-Dashboard, opendental-claude | `prompts/6-practice-numbers.md` |
| 9 | **Supply Room** | Any time | TDC-Supply-Room ✎, TDC-Office-Plan | `prompts/9-supply-room.md` |
| 10 | **Lab Tracker** | Any time | TDC-Lab-Tracker ✎, TDC-Office-Plan | `prompts/10-lab-tracker.md` |
| 11 | **Maintenance Tracker** | Any time | TDC-Maintenance-Tracker ✎, TDC-Office-Plan | `prompts/11-maintenance-tracker.md` |
| 7 | **Installer** | All modules v0.1 | TDC-Office-Installer ✎, plus all of the above | `prompts/7-installer.md` |
| 8 | **Shared Design** | Any time a look needs to change | TDC-Office-Plan ✎, TDC-Lab-Tracker ✎, TDC-Supply-Room ✎, TDC-Maintenance-Tracker ✎, TDC-Dental-Dashboard, module repos | `prompts/8-shared-design.md` |

The other repos are attached **read-only** so the agent can look at them. Agents change only their own repo. If you can attach those repos with read access only, do that.

Agents merge their own pull requests as they go, so you don't need to review each one. Watch STATUS.md, and try the sites.

## Step 3 — For each PR an agent opens

- [ ] Read the PR description. Agents are told to write a "How to test" section in plain language.
- [ ] Check that the tests passed (green check on the PR).
- [ ] For anything touching Open Dental, test on **PatNum 8133** only.
- [ ] Merge, then mark it in `STATUS.md` in TDC-Office-Plan.
- [ ] To continue an agent's work later: open its session again, or start a new one with the same kickoff message plus "Continue from STATUS.md."

## Things only you can do

- [ ] Merge opendental-claude PR #1 and PR #2 after your live test. The Connector agent builds on them.
- [ ] Confirm a BAA with Anthropic for the API (needed before Voice Charting touches real patients).
- [ ] **Wispr Flow:** sign its HIPAA BAA before dictating anything about real patients (desktop app → Settings → Data & Privacy, or in the iOS app). For the Android tablets, use a Wispr Flow team account with the BAA signed by the admin, because Android can't sign one itself. Turn off any screenshot or "context" feature you don't need, and add your dental words to Flow's dictionary.
- [ ] Decide which procedure codes count as "hygiene".
- [ ] Confirm the roles table in `SITE-OUTLINE.md`.
- [ ] Choose the web address (for example office.toledodentalcare.com) and give the Dashboard agent access to set up its DNS records when it asks. Your domain is currently with GoDaddy.
- [ ] Never paste keys or passwords into an agent chat. Agents will tell you which file to put them in on the server.
