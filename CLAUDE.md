# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Purpose

Postman workspace for the **x10s SLA (Nockhub) API** — the `member`, `access`, `guest` and `admin` Lambdas in `../x10s-sla/`.

The API specs this collection implements live in Notion:
**[Specifications database](https://app.notion.com/p/4b074c69ad384fa0b3ead93f43936767?v=31492478a0f280beb35b000c715039b8)** (Salt&Light Archery [Project] → Specifications, "Tech Specs" view). Each row is one endpoint (`User Story` = path + method) with request/response structure in the page body, grouped by `Module` (Nockhub - Members, Guest, Core, Admin Booking/User/Sales/Config/Overview, Archive). The `Nockhub - Archive` module mirrors the collection's `archive/` folder.

---

## Layout

Git is the source of truth. `.postman/resources.yaml` maps the files to the cloud workspace **x10s-sla** (`2744d549-2d56-4e88-a5ce-09b0d97e6c90`); the Postman app shows the **cloud** copy, not the files. A file edit reaches the app only after `postman workspace push`, and an app edit reaches the repo only after `postman workspace pull`. `postman/` is Postman's fixed folder convention — don't move or rename it.

| Path | Contents |
|---|---|
| `.postman/resources.yaml` | Workspace binding (`workspace.id`) and local ↔ cloud mapping. Managed by Postman. |
| `postman/collections/nockhub/` | The collection: one folder per module, one `*.request.yaml` per request, saved examples under `.resources/`. |
| `postman/collections/nockhub/archive/` | Deprecated endpoints (e.g. `access/api…`). Kept for reference, not maintained. |
| `postman/environments/` | `x10s dev` (`stage: /dev`) and `x10s prod` (`stage: ''`). Workspace-wide. |

Folder order in the sidebar comes from `order:` in each folder's `.resources/definition.yaml`.

Prefer structural changes (rename, new collection, import) in the Postman app so it keeps `resources.yaml` in sync; small edits to request YAML are fine directly.

---

## Postman CLI

Run from the repo root (`postman login` once). Common commands are listed in `README.md` § Common commands.

| Command | Agent may run? |
|---|---|
| `postman collection lint postman/collections/nockhub` | Yes — local, read-only. Run after editing request YAML |
| `postman workspace diff` | Yes — read-only local-vs-cloud preview |
| `postman collection get <id>` / `collection list -w …` / `environment list -w …` | Yes — read-only |
| `postman workspace prepare` / `push` | Only when asked — publishes to the shared cloud workspace |
| `postman workspace pull` | Only when asked — overwrites local files with the cloud copy |
| `push --push-strategy force-sync`, `collection delete`, `environment delete` | Only on an explicit request naming the target — permanent cloud deletes |

Notes:
- After a request YAML edit, the change is invisible in the app until a push. Tell the user, don't assume they'll see it.
- `push` can rewrite `.postman/resources.yaml` (new or remapped cloud IDs); commit it with the change.
- `diff` always lists `nockhub  update` with `moved from member / api-user / …` lines (file name vs request name). Not a real change; never force-sync to clear it.
- `collection example list --request "api/user"` fails: the CLI reads the `/` in request names as a folder path. Read examples from the files instead.

---

## Variables & secrets

URLs are `{{baseUrl}}{{stage}}/<module>/api/...`:
- `baseUrl` — collection variable, `https://sla.nockhub.com`
- `stage` — from the selected environment

**Never commit secrets.** Credentials are referenced, not stored:
- `x-api-key` → `{{vault:x10s-api-key}}` (Postman Vault, local only; one key for all stages)
- HitPay sandbox → `{{vault:hitpay-sandbox-api-key}}`
- Login example bodies use `<email>` / `<username>` / `<password>` placeholders
- `{{accessKey}}` is a session value pasted at runtime

Before committing, scan the diff for real keys, passwords, tokens or member PII (names, phones, emails) and replace them with variables or test values.

---

## Safety

- Requests against **x10s prod** hit live data — don't send POST/PATCH/DELETE there.
- Don't push to the remote unless asked.
