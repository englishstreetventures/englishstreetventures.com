---
title: Cloudflare deployment
tags: [runbook, cloudflare, deployment]
related:
  - "[[index]]"
  - "[[README]]"
last-reviewed: 2026-10-03
---

# Cloudflare deployment

This page records how the production Worker was bootstrapped and how to create the dedicated Cloudflare API token GitHub Actions requires. The initial Worker was deployed with the machine's existing Wrangler OAuth login; that OAuth credential is separate from the CI API token described below.

## Current setup

- Worker: `englishstreetventures-com`
- Static assets: `dist/`
- Production custom domain: `englishstreetventures.com`, configured in [`wrangler.jsonc`](../wrangler.jsonc)
- CI workflow: [`.github/workflows/deploy-production.yml`](../.github/workflows/deploy-production.yml)
- GitHub Environment: `production`
- Required Environment secrets: `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID`

The Worker was first created on 2026-10-03 using the machine's existing authorized Wrangler OAuth login. `bunx wrangler whoami` identified the account. After `bun run build`, Wrangler deployed the static assets using a temporary config containing the Worker name, `workers_dev: true`, and `assets.directory: "./dist"`, with no `routes` entry. The temporary config was removed after deployment. The first deployment is available at [englishstreetventures-com.chavaniket.workers.dev](https://englishstreetventures-com.chavaniket.workers.dev); it did not attach the production domain.

No dedicated Cloudflare API token was created during that bootstrap. The repository owner later confirmed that `CLOUDFLARE_API_TOKEN` and `CLOUDFLARE_ACCOUNT_ID` were added to the `production` GitHub Environment. The workflow checks that both values are present; a successful deploy is still needed to verify that the token permissions cover the configured Worker and domain route.

## Create the CI API token

Use an **Account API token**, not **My Profile → API Tokens**. Profile tokens are user-owned and may not show the account-level permission and resource scoping controls needed here. Creating an Account API token requires Super Administrator or API Token Provisioning access. See [Cloudflare's Account API tokens guide](https://developers.cloudflare.com/fundamentals/api/get-started/account-owned-tokens/) and [Workers permissions](https://developers.cloudflare.com/workers/authorization/workers/).

1. In the Cloudflare dashboard, select the account that owns `englishstreetventures.com`.
2. Open **Manage account → Account API tokens** and choose **Create Token**.
3. Create a custom token named for this repository's production deployment.
4. Add a Workers permission policy with the **Editor** role, scoped to the existing `englishstreetventures-com` Worker. The Worker must exist before it can be selected as an individual resource.
5. The next production deployment will attach the custom domain from `wrangler.jsonc`. For that initial attachment, add **Zone → Workers Routes → Write** scoped to the `englishstreetventures.com` zone. Cloudflare does not currently scope Custom Domain / Workers Routes access to one Worker. After the custom domain is attached, routine deployments that leave the route unchanged need only the per-Worker Editor permission.
6. Review and create the token. Copy its value when shown; Cloudflare displays it only once. Do not commit it or put it in this wiki.
7. In GitHub, open **Settings → Environments → production → Environment secrets**. Add the token as `CLOUDFLARE_API_TOKEN` and the account identifier as `CLOUDFLARE_ACCOUNT_ID`.
8. Confirm the `production` Environment contains both secrets before merging or pushing to `main`; the workflow fails its credential preflight when either is missing.

The token does not need Pages, D1, KV, R2, or user-management permissions. Cloudflare documents that deploying an existing Worker requires Editor for that Worker, while changing its route or custom domain also requires Workers Routes Write for the affected zone.

The deploy job installs Bun before invoking `cloudflare/wrangler-action`. The action detects this repository's Bun package manager and installs the pinned Wrangler version, so no application dependencies need to be installed again in the deploy job.

## Verify the bootstrap

With a valid local Wrangler login, `bunx wrangler deployments list --name englishstreetventures-com` lists the deployed version. Use `bunx wrangler whoami` to check which Cloudflare account the local OAuth login targets; it is not a check of the GitHub token. Keep CI credentials in GitHub Environment secrets and do not use a personal OAuth login as a long-term CI credential.
