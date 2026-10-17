# x10s-sla-postman

Postman collection for the x10s SLA API (`member`, `access`, `guest`, `admin` Lambdas).

**Git is the source of truth.** The Postman app is linked to this folder via
`.postman/resources.yaml` and reads/writes `postman.json` directly. Edit in
Postman, then commit here.

## Secrets

No secrets are committed. Requests reference **Postman Vault** (stored locally on
your machine, never synced to the cloud). Add these in Postman → Vault:

| Vault key | Used by |
|---|---|
| `x10s-api-key` | `x-api-key` header on all x10s requests |
| `hitpay-sandbox-api-key` | `guest / (hitpay) payment-requests` |

The `api/auth/login` examples use `<email>` / `<username>` / `<password>`
placeholders — type real credentials when testing, but don't save them.

`{{accessKey}}` (used in `api/misc/auth`) is a session value — paste it from a
login response or set it in your environment.

## Variables

| Variable | Where | Value |
|---|---|---|
| `baseUrl` | collection | `https://sla.nockhub.com` |
| `stage` | `environments/dev` | `/dev` → `https://sla.nockhub.com/dev/member/...` |
| `stage` | `environments/prod` | `""` → `https://sla.nockhub.com/member/...` |

Environments aren't auto-synced from this folder. Postman → **Import** → select
both files in `environments/` (once, or again after changing them). Then pick
**x10s dev** or **x10s prod** from the environment dropdown. Careful on prod —
POST/PATCH/DELETE hit live data.

## Before committing

Make sure Postman hasn't written a real key back into the file:

```sh
git diff | grep -iE 'x-api-key|password|secret' -A2
```
