# strake-dev/gate-check

> Part of [Strake](https://strake.dev) — CI-native deploy safety.

Pre-deploy safety check for GitHub Actions. Runs on `pull_request` (or anywhere
you want a verdict), calls the Strake gate engine, and upserts a verdict
comment on the PR. Optionally blocks the merge on HOLD or CRITICAL.

**Need an account?** Sign up free at [strake.dev](https://strake.dev).

## Usage

```yaml
# .github/workflows/strake-gate.yml
name: Strake Gate Check
on:
  pull_request:
    types: [opened, synchronize, reopened]

permissions:
  pull-requests: write  # required for the PR comment

jobs:
  gate:
    runs-on: ubuntu-latest
    steps:
      - uses: strake-dev/gate-check@v1
        with:
          strake-deploy-token: ${{ secrets.STRAKE_DEPLOY_TOKEN }}
          service-id: 00000000-0000-0000-0000-000000000000
          fail-on-hold: false   # optional — set true to block HOLD merges
```

That's three lines after the boilerplate. Obtain `STRAKE_DEPLOY_TOKEN` from
[**Settings → Deploy Tokens**](https://strake.dev/app/settings/deploy-tokens)
and the service UUID from
[**Settings → Services**](https://strake.dev/app/settings/services).

## Inputs

| Name | Required | Default | Description |
|---|---|---|---|
| `strake-deploy-token` | yes | — | Deploy token (bearer auth) |
| `service-id` | yes | — | Strake service UUID |
| `strake-api-url` | no | `https://api.strake.dev` | Override only for self-hosted |
| `fail-on-hold` | no | `false` | Exit non-zero on HOLD |
| `post-pr-comment` | no | `true` | Upsert a PR comment with the verdict |
| `github-token` | no | `${{ github.token }}` | Token used to post the comment |

## Outputs

| Name | Description |
|---|---|
| `verdict` | `go` / `hold` / `critical` |
| `reason` | Human-readable reason from the gate engine |
| `service` | Service slug |
| `checked-at` | ISO-8601 timestamp of the evaluation |

## Exit codes

| Verdict | `fail-on-hold=false` | `fail-on-hold=true` |
|---|---|---|
| `go` | 0 | 0 |
| `hold` | 0 (warning annotation) | 1 |
| `critical` | 1 | 1 |

Network errors or malformed responses → exit 2.

## Links

- [strake.dev](https://strake.dev) — product homepage
- [Docs](https://strake.dev/docs) — setup guides and API reference
- [Dashboard](https://strake.dev/app/gate) — view live gate evaluations
- [Issues](https://github.com/strake-dev/gate-check/issues) — report bugs or request features

---

© 2026 Strake. MIT licensed.
