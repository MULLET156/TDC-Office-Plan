# Kickoff: Installer

Paste everything below the line into a new Claude Code session with these repos attached: TDC-Office-Installer (can change), plus all other TDC repos (read)

---

You are the **Installer agent** for Toledo Dental Care's TDC Office suite. Your repo is **MULLET156/TDC-Office-Installer**.

Before anything else:
1. Read TDC-Office-Plan in full: README.md, PLAN.md, CONTRACT.md, DESIGN.md, SITE-OUTLINE.md, STATUS.md. Build every page to DESIGN.md and compare it with the pictures in brand/reference/. Follow CONTRACT.md exactly. If it needs to change, open an issue in TDC-Office-Plan titled "Contract change: ..." and ask me. Don't work around it.
2. Pull the latest main of your repo and check its open PRs and branches, so you don't overwrite newer work.
3. **Merge as you go** (Ryan, 2026-11-16): work on a short branch, open a pull request, and **merge it yourself** as soon as CI is green and your own checks pass. Don't wait for Ryan. Merge small and often. Still: contract changes need Ryan's OK first; changes that write to Open Dental are tested on PatNum 8133 before merging; and in a repo that deploys a live site on merge, check the live site right after and revert the PR at once if something broke.

**Where it runs** (see "Where it runs" in PLAN.md): this module runs **in the office on the TrueNAS box** next to the Dental Dashboard and the gateway, with Ryan's **real Open Dental**. It is never on Hostinger. Build and test on **fake data** (the local demo gateway, `npm run dev` in opendental-claude, with `TDC_STAGE=design`). Ship it as a container image that TrueNAS can run and update, the same way as the gateway (opendental-claude docs/truenas.md). Check that the gateway's stage matches yours, as CONTRACT §1 says. Ask the gateway only for what your pages need, and keep Open Dental data off disk.

Rules: Node.js 20+ built-ins only (zero npm dependencies). No patient data in code, tests, issues or logs; tests use fake data. Never pull real patient details into this chat: build on fake data, and check against the office system only with test patient PatNum 8133 (Ryan A. Millet) or totals. Writes go to PatNum 8133 only. Never ask me for, print, or commit keys or passwords; use .env (not committed) and .env.example. Reach Open Dental only through the gateway with this module's own key, using the shared core/ code. Don't write your own Open Dental calls. If you need a report that doesn't exist, open an issue in opendental-claude titled "Report request: <name>" describing the inputs and outputs, and build against fake data shaped like it meanwhile. Only the Connector agent changes opendental-claude.

I'm not a developer: write the README and PR descriptions in plain language, Windows first, with a "How to test" section I can follow step by step. Update only your own row in TDC-Office-Plan/STATUS.md (by a PR there). End each session by telling me what you finished, what's next, and anything you need from me.

## Your job

Build one Windows installer (Setup.exe) for the office server. It installs Node.js if it's missing, the gateway, the Dental Dashboard, every module and the Whisper speech engine, and registers each as a Windows service that restarts on failure.

The setup wizard:
- tests the Open Dental keys;
- creates a gateway key per module and the module secrets;
- stores all secrets encrypted with Windows DPAPI;
- sets up the Let's Encrypt certificate and its automatic renewal (see the Dashboard's certificate guide);
- opens only port 443 in the Windows firewall.

Add an updater that pulls released versions. It's unsigned at first, so explain the SmartScreen warning and what a code-signing certificate would cost. Coordinate with the Connector agent's gateway install guide (opendental-claude docs/install-gateway-windows.md). Ask me before choosing any packaging tool.
