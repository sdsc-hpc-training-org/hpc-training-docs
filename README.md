# HPC Training Docs Website

This repository hosts the SDSC HPC training docs site built with [Docusaurus](https://docusaurus.io/).

## How It Works

- Documentation source repos are tracked as git submodules under `docs_external/`.
- Submodules are synced from your GitHub org by topic (`documentation`) using `scripts/sync-doc-submodules.mjs`.
- External docs are patched/copied into `docs/` by `scripts/patch-external-docs.mjs`.
- Docusaurus serves/builds from `docs/`.

Key config files:

- `submodules.docs.json` - org/topic/path/branch rules for sync
- `.gitmodules` - tracked submodule entries
- `docusaurus.config.ts` - site/nav/theme config

## Prerequisites

- Node.js `>=20`
- npm

## Install

```bash
npm install
```

## Run Locally

```bash
npm run start
```

`prestart` automatically runs `scripts/patch-external-docs.mjs` first.

## Build Locally

```bash
npm run build
```

`prebuild` also runs `scripts/patch-external-docs.mjs` first and outputs static files to `build/`.

## Sync Submodules Locally

To test org/topic auto-sync locally:

```bash
GH_TOKEN=<your_github_token> node scripts/sync-doc-submodules.mjs
```

Then check what changed:

```bash
git status
cat .gitmodules
ls docs_external
```

## GitHub Actions (Single Workflow)

Workflow: `.github/workflows/sync-doc-submodules.yml`

Triggers:

- push to `main`
- daily schedule (`0 9 * * *`)
- manual dispatch

Pipeline in one workflow:

1. Sync submodules from org topic + manual repos
2. Commit and push submodule changes (if any)
3. Build Docusaurus site
4. Deploy to GitHub Pages

## Notes

- External repo inclusion rules are controlled by `submodules.docs.json`.
- Repos discovered by org topic are auto-removed when they no longer match the configured topic (`autoRemove: true`).
- `manualRepos` are always included and never auto-removed.
- Docs patching is idempotent and reruns on every start/build.
