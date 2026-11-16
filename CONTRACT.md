# Module contract

Every module follows this contract so the Dashboard can show it as part of one site. **Changing this file needs Ryan's OK** (open an issue in this repo titled `Contract change: ...`). Contract version: **1**.

## 1. Ports and addresses

Every module reads these settings: `TDC_STAGE` (`design` or `live`), `PORT`, `MODULE_SECRET`, `GATEWAY_URL`, `GATEWAY_KEY`. The Dental Dashboard keeps each module's **base URL** in its settings. Nothing hard-codes `127.0.0.1` or a port.

**Design stage (Hostinger):**
- Each module is its own Hostinger Node.js app on its own subdomain, for example `daysheet-dev.<domain>`. It listens on the `PORT` Hostinger gives it.
- The Dental Dashboard passes requests to the module's HTTPS base URL.
- Because the module can be reached from the internet, it must reject every unsigned request except `/health`, exactly as in §3.
- `GATEWAY_URL` points to the office gateway through its tunnel address (HTTPS). Each module checks that the gateway's `/health` says `"stage": "design"`, at startup and every 5 minutes, and stops serving patient pages if it doesn't. The local demo gateway (fake data) also says `"stage": "design"`.
- Modules on Hostinger keep Open Dental data in memory only (short cache, a few minutes at most), never on disk.

**Live stage (office server)** uses the table below.

| Service | Listens on | Reached by |
|---|---|---|
| Dashboard | `0.0.0.0:443` HTTPS (dev: `127.0.0.1:8443`) | Office browsers |
| Daysheet | `127.0.0.1:3101` HTTP | Dashboard only |
| Treatment Finder | `127.0.0.1:3102` | Dashboard only |
| Voice Charting | `127.0.0.1:3103` | Dashboard only |
| Practice Numbers | `127.0.0.1:3104` | Dashboard only |
| Speech engine (Whisper) | `127.0.0.1:3110` | Voice Charting only |
| Gateway | `127.0.0.1:8787` | Modules only |
| Next modules | `3105`–`3109` | Assigned in this file |

In the live stage, modules **must** listen on `127.0.0.1` only. The port comes from `PORT`, defaulting to the value above.

## 2. Pass-through

The Dashboard passes `https://tdc-office/m/<module-id>/<rest>` to `http://127.0.0.1:<port>/<rest>`, removing the `/m/<module-id>` prefix. Modules build links with the base path from the `X-TDC-Base` header (for example `/m/daysheet`), or use relative links.

Request and response bodies are passed through unchanged, up to 25 MB (audio uploads), along with `Content-Type`. The Dashboard sets the security headers on every response.

- The browser's cookies and `Authorization` header are not passed to modules, and `Set-Cookie` from a module is dropped. Keep per-user state on the server, keyed by `X-TDC-User`.
- A module redirect to `/x` is rewritten to `/m/<module-id>/x`. Redirects to other sites are dropped.

## 3. Login and identity

Only the Dashboard handles logins. It strips any incoming `X-TDC-*` headers from the browser, then adds these to every request it passes on:

| Header | Example |
|---|---|
| `X-TDC-User` | `jsmith` (login name; never a patient) |
| `X-TDC-Name` | `Jane Smith` (display name) |
| `X-TDC-Roles` | `doctor,owner` (comma-separated, from the list below) |
| `X-TDC-Base` | `/m/daysheet` |
| `X-TDC-Time` | Unix time in milliseconds |
| `X-TDC-Sig` | hex HMAC-SHA256 (see below) |

**Signature:** `HMAC-SHA256(MODULE_SECRET, time + "\n" + method + "\n" + pathAndQuery + "\n" + user + "\n" + roles + "\n" + base)`
- The HMAC key is the `MODULE_SECRET` string exactly as written in `.env` (the hex text itself, as UTF-8), not the decoded bytes.
- `pathAndQuery` is the request target exactly as the module receives it (`req.url` in Node): the path after the prefix is removed, plus the query string.
- The six values are joined with a single `\n`; `method` is upper-case. The result is lower-case hex.
- `X-TDC-Name` is not signed. Names with non-ASCII letters (José) are percent-encoded; decode with `decodeURIComponent`.
- Reference code: `examples/hello-module/tdc-verify.js` in TDC-Dental-Dashboard. Check a module with `scripts/check-module.js` there.
- Each module has its own `MODULE_SECRET`, at least 32 random bytes in hex, in its `.env`. The Dashboard holds a copy of each one in its own config.
- A module **must reject** with `401` any request whose signature is wrong or missing, or whose time is more than 30 seconds from now. Compare signatures with `crypto.timingSafeEqual`.
- Exceptions: `GET /health` needs no signature. Module-to-module calls are not allowed; go through the Dashboard or the gateway.
- The Dashboard calls `/manifest.json` as the system user `dashboard` with role `system`.
- The Dashboard calls `/tiles/*` **as the logged-in staff member** (their own `X-TDC-User` and `X-TDC-Roles`), so a tile can be personal, like *My drafts waiting*. It only asks for a tile when the user has one of the tile's `roles`; the module still checks roles itself.

**Roles:** `owner`, `admin`, `doctor`, `hygienist`, `assistant`, `front`. A module checks roles itself for each page and action.

**Sessions** (Dashboard): an HttpOnly, Secure, `SameSite=Strict` cookie. Sessions log out after 12 hours, or after a role-dependent idle time (default 30 minutes). Each user has a password, plus an optional 4–6 digit PIN for quick unlock on a shared tablet. For any request other than GET or HEAD, the Dashboard checks that `Origin` matches its own site.

## 4. Manifest

Each module serves `GET /manifest.json`:

```json
{
  "contract": 1,
  "id": "daysheet",
  "name": "Dental Daysheet",
  "version": "0.1.0",
  "description": "Today's patients with unscheduled treatment and x-ray status",
  "icon": "calendar",
  "roles": ["owner", "admin", "doctor", "hygienist", "assistant", "front"],
  "nav": [
    { "label": "Today", "path": "/" },
    { "label": "Unscheduled treatment", "path": "/unscheduled" }
  ],
  "tiles": [
    { "id": "today", "title": "Today's schedule", "path": "/tiles/today", "roles": ["owner","admin","doctor","hygienist","assistant","front"], "refreshSeconds": 120 }
  ]
}
```

`icon` is one name from the shared icon set (`calendar`, `search`, `mic`, `chart`, `flask`, `box`, `wrench`, `home`, `settings`).

## 5. Tiles

`GET /tiles/<id>` returns JSON that the Dashboard renders. Modules never send HTML for tiles.

```json
{
  "title": "Today's schedule",
  "value": "23 patients",
  "status": "ok",
  "items": [ { "text": "8:00 Smith, J. — crown seat", "href": "/" } ],
  "link": "/",
  "updated": "2026-11-16T08:00:00-08:00"
}
```

- `status` is `ok`, `warn` or `alert`.
- `items` holds at most 8 entries.
- `href` values are module-relative; the Dashboard adds `/m/<id>`.
- Tiles must answer within 3 seconds. Cache them if needed (per user, if the tile depends on who is asking).
- Patient names are allowed on tiles, since the site is office-only. Never include phone numbers, birthdates or SSNs.


## 5b. Tiles from standalone apps

The standalone PHP apps (Lab Tracker, Supply Room, Maintenance Tracker; PLAN.md "Standalone apps") run on their own sites with their own logins, not behind the Dashboard. They serve tiles this way instead of §5's pass-through (Ryan, 2026-11-16, issue #23).

- **Address:** `GET https://<app site>/tdc/tiles/<id>`. The answer is §5's tile JSON (`title`, `value`, `status`, `items`, `link`, `updated`).
- **Full links:** `link` and every `items[].href` are full `https://` URLs into the app. The Dashboard opens them as they are; staff sign in to that app as usual.
- **Server to server only:** the Dashboard calls from its server, never from the browser, with `Authorization: Bearer <TILE_TOKEN>`.
- **The token:** each app has its own `TILE_TOKEN` (at least 32 random bytes, hex), kept in the app's server-side config and in the Dashboard's settings, never in a repo, log or chat. The app compares it in constant time (`hash_equals`).
- **Answers:** `401` for a missing or wrong token. `404` when the app has no `TILE_TOKEN` set (tiles turned off). `429` when rate-limited (about 60 requests a minute per app). Every answer has `Cache-Control: no-store`. Answer within 3 seconds; the app may cache its own counts for up to a minute.
- **Content:** counts and short labels. **No patient names** on any standalone tile (they run on Hostinger, which has no BAA), and no phone numbers, birthdates, prices or notes. An app may leave `items` empty.
- **Logs:** nothing logged except the tile id and the status code.
- **Who sees it:** the tile is the same for every user, since the app doesn't know who is asking. The Dashboard decides which roles see it. Standalone apps have no manifest (§4), so the Dashboard keeps a list in its own settings: app name, base URL, tile ids, roles, accent and token.
- **Not responding:** as DESIGN.md says, the tile shows "Not responding" and the rest of the page keeps working.

| App | Tile ids |
|---|---|
| Lab Tracker | `due-week`, `late`, `to-check` |
| Supply Room | `to-order`, `waiting` |
| Maintenance Tracker | `overdue`, `due-week` |

## 6. Shared look

The Dashboard serves the shared pieces, and every module page includes them:

```html
<link rel="stylesheet" href="/shared/tdc.css">
<script src="/shared/shell.js" defer></script>
<body data-module="daysheet"> ... <main class="tdc-main"> page content </main>
```

- `shell.js` draws the header: logo, module nav (from `/api/nav`), user name and log out.
- `GET /api/me` returns `{ user, name, roles }`.
- **Follow [DESIGN.md](DESIGN.md)** for colours, fonts, logo, header, status tiles, rows and the phone layout. `data-module` on `<body>` sets the module's accent colour.
- Use the CSS variables and classes in `tdc.css`: `--tdc-*` colours, `.tdc-card`, `.tdc-table`, `.tdc-btn`, `.tdc-btn-primary`, `.tdc-badge-ok|warn|alert`, `.tdc-form`, plus the list classes in DESIGN.md §8 (`.tdc-stats`, `.tdc-pagehead`, `.tdc-find`, `.tdc-seg`, `.tdc-row`, `.tdc-chip`, `.tdc-meter`, `.tdc-hbtn`). Page buttons go in `<nav class="tdc-actions">`; `shell.js` places them in the header.
- Don't ship your own fonts, colours or frameworks. The Dashboard serves the fonts from `/shared/fonts/`; never load fonts from an outside site.
- **No inline scripts.** The Dashboard's security policy (`script-src 'self'`) blocks `<script>…</script>` with code inside and `on…=` attributes such as `onclick`. Load scripts from files with `<script src="…" defer>`. Inline `style="…"` is fine.
- Requests a page makes **on a timer** (auto-refresh, polling) send the header `X-TDC-Background: 1`, so a screen left open doesn't keep an idle user logged in.
- Pages must work at tablet width (768px) and up, with touch targets at least 44px. Content must not break at phone width (390px): `shell.js` handles the phone header and bottom bar.

## 7. Open Dental

- Every module uses its **own gateway key**, created with `node gateway/cli.js new-key "<module-id>"` in opendental-claude. Each key has the narrowest allow list that works.
- Use the shared `core/` code, copied into `vendor/opendental-core/` at a released opendental-claude version. `vendor/opendental-core/VERSION` records the version.
- Update the copy with `node scripts/sync-core.js <version>` (provided by the Connector agent). Never edit vendored files.
- Reports run through the gateway's `/v1/reports/<name>`. Any new SQL goes in `opendental-claude/core/reports.js`, written by the Connector agent.

## 8. Health, logs, config

- `GET /health` (no signature) returns `{ "ok": true, "version": "0.1.0", "contract": 1 }`.
- **Logs:** one JSON line per request with time, user, method, path, status and milliseconds. No patient names, no request bodies, no notes text.
- **Config:** `.env` (never committed) plus `.env.example` (committed, no real values).
- **Required env:** `TDC_STAGE`, `PORT`, `MODULE_SECRET`, `GATEWAY_URL` (default `http://127.0.0.1:8787`), `GATEWAY_KEY`.
- **Data files** (users, saved lists, drafts) go under `data/`, which is never committed. They never hold copies of Open Dental data: only the module's own records, referring to patients by PatNum.
- **Start:** `npm start`, which runs `node server.js`. **Test:** `npm test`, which runs `node --test` with fake data only.
- **Windows:** each repo includes `start.cmd` for running by hand. Windows services come later, from the Installer.

## 9. Required in every module repo

- `CLAUDE.md`: points to this plan and lists the module's own rules
- `README.md`: plain-language setup for Windows
- `.env.example`
- `server.js`, `manifest.json` (or generated), `test/`
- `vendor/opendental-core/` once the module reads Open Dental
- CI that runs `npm test` on Windows and Linux

## Changes

- 2026-11-16: new §5b, tiles from the standalone apps (Lab Tracker, Supply Room, Maintenance Tracker): `/tdc/tiles/<id>`, Bearer `TILE_TOKEN`, full links, counts and short labels, no patient names (issue #23, approved by Ryan). Still contract version 1: additive.
- 2026-11-16: §6 now points to DESIGN.md, the shared design Ryan approved (warm base from the logo, one accent per module, Archivo/Figtree/JetBrains Mono served by the Dashboard, new list classes, phone layout). Still contract version 1: additive for modules.
- 2026-11-16: the design stage uses Ryan's Open Dental **demo database** through the office gateway and a secure tunnel. The stage guard is now the gateway's `"stage"` (`design`/`live`) instead of `"fake": true`. No Open Dental data on disk on Hostinger. Still contract version 1.
- 2026-11-15: clarified the signature (key, `pathAndQuery`), tiles are requested as the logged-in user, pass-through cookie and redirect rules, no inline scripts, `X-TDC-Background` (issues #6, #7, #8). Still contract version 1: no module had been built yet.
