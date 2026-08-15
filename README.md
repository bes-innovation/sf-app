# Salesforce project template

Starter repo for new Salesforce projects on the GDM DevOps platform. Comes
with the QA validate/deploy pipeline already wired to the shared workflow
library — [bes-innovation/gmd-seed-gh-workflow](https://github.com/bes-innovation/gmd-seed-gh-workflow).

## What's included

- `.gitignore` — includes `.certs/` (never commit JWT private keys)
- `.github/workflows/pr-validation-qa.yml` — PMD scan + check-only validate on PRs into `development`
- `.github/workflows/deploy-qa.yml` — deploys `force-app` to `qa` on merge to `development`

Both call the reusable workflows in `gmd-seed-gh-workflow@v1` rather than
duplicating pipeline logic. See that repo's README for what each workflow
does and every available input.

## Setup checklist for a new project

1. **Scaffold the SFDX project** (not included here — this template is
   CI/CD only): `sf project generate --name <project-name> --manifest --output-dir .`
2. **Generate a JWT cert**: `openssl req -x509 -newkey rsa:2048 -keyout .certs/server.key -out .certs/server.crt -days 365 -nodes`
3. **Create a Connected App / External Client App** in the target org, upload the cert, enable JWT Bearer Flow, pre-authorize a dedicated integration user via a Permission Set.
4. **Create a `qa` GitHub Environment** on this repo (Settings → Environments), then set:
   - `SFDX_USERNAME`, `SFDX_CLIENT_ID`, `SFDX_INSTANCE_URL` (Environment secrets)
   - `SFDX_JWT_KEY` (Repository secret, or Environment if the Connected App differs per org)
   - `TEAMS_WEBHOOK_URL` (optional — deploy notifications are skipped if unset)
5. **Verify locally** before relying on CI: `sf org login jwt --username ... --client-id ... --jwt-key-file .certs/server.key --instance-url ...`

## Adding UAT / PROD later

Not included by default — add when the project is actually ready for
those stages, following the examples in `gmd-seed-gh-workflow`'s README
(`gmd-seed-wfl-sf-validate.yml` / `gmd-seed-wfl-sf-deploy.yml` with
`environment: uat` / `prod`, plus `use-quick-deploy: true` and
backpromotion for prod). Each needs its own GitHub Environment and
secrets, following the same pattern as `qa` above.

<!-- rc-ladder probe: merged into release/v0.1.0 to verify the candidate counter increments without moving the base version. Safe to delete. -->
