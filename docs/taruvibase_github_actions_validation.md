# TaruviBase GitHub Actions Deploy Validation

## Workflow

- File: `.github/workflows/deploy.yml`
- Triggers: push to `main` or `dev`, plus manual `workflow_dispatch`
- Environment selection: `environment: ${{ github.ref_name }}`
- Frontend action: `Taruvi-ai/taruvi-action/frontend-worker@v1`
- Backend action: `Taruvi-ai/taruvi-action/backend@v1`

## Required GitHub Environment Values

Create GitHub environments named exactly after the branch that deploys.

For each environment, set:

- Secret: `TARUVI_API_KEY`
- Variable: `TARUVI_SITE_URL`
- Variable: `TARUVI_APP_SLUG`
- Variable: `TARUVI_APP_TITLE`

The API key must come from the same TaruviBase site as `TARUVI_SITE_URL`.

## Repo Compatibility Checks

- `package-lock.json` exists, so `npm ci` is valid.
- `package.json` has a `build` script and should produce `dist/`.
- `vite.config.ts` reads both `TARUVI_*` and `VITE_TARUVI_*` values during build.
- The workflow does not pass `TARUVI_API_KEY` into the frontend build.
- No `.taruvi-backend/` directory exists currently, so backend import is expected to report `skipped`.

## First Validation Run

After committing the workflow to the target branch:

```bash
gh workflow run deploy.yml --ref dev
gh run watch
gh run view --log-failed
```

Expected result:

- Install step completes with `npm ci`.
- Build step creates `dist/`.
- ZIP step creates `dist.zip`.
- Frontend worker action uploads and activates the build.
- Backend action reports `skipped` when `.taruvi-backend/` is absent.
- Summary prints the frontend URL and backend status.
