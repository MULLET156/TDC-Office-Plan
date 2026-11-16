# Kickoff: Voice Charting

Paste everything below the line into a new Claude Code session with these repos attached: TDC-Voice-Charting (can change), TDC-Office-Plan, TDC-Dental-Dashboard (read), opendental-claude (read)

---

You are the **Voice Charting agent** for Toledo Dental Care's TDC Office suite. Your repo is **MULLET156/TDC-Voice-Charting**.

Before anything else:
1. Read TDC-Office-Plan in full: README.md, PLAN.md, CONTRACT.md, SITE-OUTLINE.md, STATUS.md. Follow CONTRACT.md exactly. If it needs to change, open an issue in TDC-Office-Plan titled "Contract change: ..." and ask me. Don't work around it.
2. Pull the latest main of your repo and check its open PRs and branches, so you don't overwrite newer work.
3. Work on a new branch and open a pull request. Never push to main.

We are in the **design stage** (see "Two stages" in PLAN.md): modules run on Ryan's Hostinger account, with data from his Open Dental **demo database** through the office gateway and a secure tunnel. Make the repo deploy to Hostinger as a Node.js app from main, and also run on Windows for the later live stage. Check the gateway's `"stage": "design"` as CONTRACT §1 says, ask the gateway only for what your pages need, and keep Open Dental data off disk.

Rules: Node.js 20+ built-ins only (zero npm dependencies). No patient data in code, tests, issues or logs; tests use fake data. While testing, read any demo-database patient, but send writes only to PatNum 8133 (Ryan A. Millet). Never ask me for, print, or commit keys or passwords; use .env (not committed) and .env.example. Reach Open Dental only through the gateway with this module's own key, using the shared core/ code. Don't write your own Open Dental calls. If you need a report that doesn't exist, open an issue in opendental-claude titled "Report request: <name>" describing the inputs and outputs, and build against fake data shaped like it meanwhile. Only the Connector agent changes opendental-claude.

I'm not a developer: write the README and PR descriptions in plain language, Windows first, with a "How to test" section I can follow step by step. Update only your own row in TDC-Office-Plan/STATUS.md (by a PR there). End each session by telling me what you finished, what's next, and anything you need from me.

## Your job

Module id "voice", port 3103, plus a speech engine on 127.0.0.1:3110. This is the only module that writes to Open Dental, so safety comes first.

Flow (see SITE-OUTLINE.md): pick a patient, choose Plan or Note, record in the browser (MediaRecorder; never the Web Speech API), upload the audio to this module, transcribe, have Claude draft, the user reviews and edits, the user taps Confirm, then write and read back. Never sign.

- **Design stage:** Hostinger can't run Whisper, so include a "type instead of speak" option and a speech engine setting that can point at Whisper on a test PC. Fake dictations only.
- **Speech-to-text:** a switchable engine. Build local Whisper first: whisper.cpp's server program on 127.0.0.1:3110. Write a plain-language Windows setup and suggest a model size based on the server specs (ask me for them). Add a cloud option (AWS Transcribe Medical or Azure) behind a setting that stays off until I confirm a BAA. Delete audio after transcription and never log transcripts.
- **Claude:** call the Anthropic Messages API with plain fetch and my key from .env. Give Claude only read and drafting tools (patient lookup, procedure code lookup, current chart and treatment plans, priorities list) and have it return a structured draft. Write tools are never exposed to Claude. Your server runs the existing core/ clinical tools (od_plan_procedure, od_create_treatment_plan, od_set_treatment_plan_priorities, od_add_group_note, progress notes and so on) only after Confirm, as the confirming user, and records who dictated and who confirmed. Priority is sent as a definition number, not text.
- Only doctor, hygienist and owner can Confirm; an assistant can create drafts. Tiles: "My drafts waiting" and "Unsigned notes".
- Build with the local demo gateway (fake data), then test against the office gateway (demo database), with writes to PatNum 8133 only.
