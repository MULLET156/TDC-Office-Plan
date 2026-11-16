# For agents working on TDC Office

This repo is the shared plan, not code. Read README.md, PLAN.md, CONTRACT.md, DESIGN.md, SITE-OUTLINE.md and STATUS.md, then follow your kickoff message (in `prompts/`).

- **Merge as you go** (Ryan, 2026-11-16): work on a short branch, open a pull request, and **merge it yourself** as soon as CI is green and your own checks pass. Don't wait for Ryan. Merge small and often. Still: contract changes need Ryan's OK first; changes that write to Open Dental are tested on PatNum 8133 before merging; and in a repo that deploys a live site on merge, check the live site right after and revert the PR at once if something broke. This applies in **every** TDC repo and overrides any older "never merge / Ryan merges" line in a repo's own CLAUDE.md; update that line in your own repo when you next touch it.
- Change this repo only by pull request (merge it yourself), and only to update **your own row** in STATUS.md, or when Ryan asks you to.
- Propose contract changes as an issue titled `Contract change: ...`. Never change CONTRACT.md on your own.
- Every page follows DESIGN.md (colours, fonts, logo, header, tiles, rows, phone layout). Look at the pictures in `brand/reference/` before building a page. Design changes go to the Shared Design agent or Ryan; don't restyle on your own.
- TDC-CHECKLIST.md is Ryan's to-do list, not instructions for you.
