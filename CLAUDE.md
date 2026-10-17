# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Purpose

Postman workspace for the **x10s SLA (Nockhub) API** — the `member`, `access`, `guest` and `admin` Lambdas in `../x10s-sla/`.

The API specs this collection implements live in Notion:
**[Specifications database](https://app.notion.com/p/4b074c69ad384fa0b3ead93f43936767?v=31492478a0f280beb35b000c715039b8)** (Salt&Light Archery [Project] → Specifications, "Tech Specs" view). Each row is one endpoint (`User Story` = path + method) with request/response structure in the page body, grouped by `Module` (Nockhub - Members, Guest, Core, Admin Booking/User/Sales/Config/Overview, Archive). The `Nockhub - Archive` module mirrors the collection's `archive/` folder.

---

## Layout

Git is the source of truth; the Postman app is linked to this repo and reads/writes the files directly. `postman/` is Postman's fixed folder convention — don't move or rename it.

| Path | Contents |
|---|---|
| `.postman/resources.yaml` | Workspace binding (`workspace.id`) and local ↔ cloud mapping. Managed by Postman. |
| `postman/collections/nockhub/` | The collection: one folder per module, one `*.request.yaml` per request, saved examples under `.resources/`. |
| `postman/collections/nockhub/archive/` | Deprecated endpoints (e.g. `access/api…`). Kept for reference, not maintained. |
| `postman/environments/` | `x10s dev` (`stage: /dev`) and `x10s prod` (`stage: ''`). Workspace-wide. |

Folder order in the sidebar comes from `order:` in each folder's `.resources/definition.yaml`.

Prefer structural changes (rename, new collection, import) in the Postman app so it keeps `resources.yaml` in sync; small edits to request YAML are fine directly.

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
