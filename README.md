# x10s-sla-postman

Postman collection for the x10s SLA API (`member`, `access`, `guest`, `admin` Lambdas).

**Git is the source of truth.** `.postman/resources.yaml` maps the files under
`postman/` (Postman's fixed layout) to the cloud workspace **x10s-sla**. The
Postman app shows the **cloud** copy, so file changes only appear in the app
after `postman workspace push`, and edits made in the app only reach the repo
after `postman workspace pull`.

```
postman/collections/nockhub/   ← the collection, one YAML file per request
postman/environments/          ← x10s dev / x10s prod
```

## Common commands

Postman CLI (`postman`), run from the repo root. Log in once with `postman login`.

| Task | Command |
|---|---|
| Check the collection files for errors | `postman collection lint postman/collections/nockhub` |
| Preview what differs between repo and cloud (read-only) | `postman workspace diff` |
| Send repo changes to the app (cloud) | `postman workspace prepare` then `postman workspace push` |
| Bring edits made in the app into the repo | `postman workspace pull`, then review `git diff` and commit |
| List cloud collections / environments | `postman collection list -w 2744d549-2d56-4e88-a5ce-09b0d97e6c90` / `postman environment list -w 2744d549-2d56-4e88-a5ce-09b0d97e6c90` |

Typical loop after editing request YAML here: `lint` → `diff` → `push` → commit.
After editing in the app: `pull` → review → commit.

- `push` rewrites `.postman/resources.yaml` when it creates or remaps cloud
  objects. Commit that file with your change.
- **Never** `push --push-strategy force-sync` unless you mean it: it deletes every
  cloud item that has no file here (currently the WORKSPACE-README document).
- `diff` always shows `nockhub  update` with `moved from member / api-user / …`
  lines. That's file names vs request names, not a real change.

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
