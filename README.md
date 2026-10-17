# x10s-sla-postman

Postman collection for the x10s SLA API (`member`, `access`, `guest`, `admin` Lambdas).

**Git is the source of truth.** The Postman app is linked to this folder via
`.postman/resources.yaml` and reads/writes everything under `postman/`
(Postman's fixed layout). Edit in Postman, then commit here.

```
postman/collections/nockhub/   ← the collection, one YAML file per request
postman/environments/          ← x10s dev / x10s prod
```

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
| `stage` | `postman/environments/x10s dev` | `/dev` → `https://sla.nockhub.com/dev/member/...` |
| `stage` | `postman/environments/x10s prod` | `''` → `https://sla.nockhub.com/member/...` |

Pick **x10s dev** or **x10s prod** from the environment dropdown. Careful on
prod — POST/PATCH/DELETE hit live data.

## Before committing

Make sure no real key or password was saved into a request:

```sh
git diff | grep -iE 'x-api-key|password|secret' -A2
```
