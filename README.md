# renovate-config — Octet / OneVision shared Renovate preset

Single source of truth for Renovate behaviour across OneVision-io repos.

## What it does
- `rebaseWhen: behind-base-branch` — conflicting bot PRs auto-rebase (no more stale/CONFLICTING pile-up).
- Major updates disabled org-wide (open/review manually).
- `hostRules` for `npm.pkg.github.com` so Renovate can auth to GitHub Packages and **regenerate lockfiles** that reference `@octetsolutions/*` — this fixes `renovate/artifacts: Artifact file update failure`.

## How repos consume it
Each repo's `renovate.json`:
```json
{ "extends": ["github>Onevision-io/renovate-config"] }
```

## ⚠️ ONE manual step (owner-only) — Mend-encrypt the registry token
The hosted Mend Renovate app cannot read GitHub Actions secrets; the token must be encrypted with Mend's key.

1. Get the token value: `cckey get github/classic` (classic `ghp_` PAT, has `read:packages`). Optionally mint a dedicated read-only packages token instead.
2. Go to https://app.renovatebot.com/encrypt — set **Organization = Onevision-io**, paste the token as the value, click Encrypt.
3. Copy the encrypted string and replace `PASTE_MEND_ENCRYPTED_TOKEN_HERE` in `default.json` → `hostRules[0].encrypted.token`. Commit.

Until step 3 is done, lockfile regeneration for private-package bumps will still fail; `rebaseWhen` and major-disabled work immediately.

## Reusable workflow: lockfile-only fast-path

Short-circuits CI to a 30-60s smoke when the ONLY changed files are lockfiles (`package-lock.json`, `pnpm-lock.yaml`, `yarn.lock`, `pubspec.lock`, `composer.lock`, `Cargo.lock`). Runs `npm/pnpm/yarn ci --prefer-offline` and `tsc --noEmit` if a tsconfig is present. Otherwise emits `heavy_needed=true` so the caller runs full CI.

Opt-in from any repo's CI:
```yaml
jobs:
  fast-path:
    uses: Onevision-io/renovate-config/.github/workflows/lockfile-fast-path.yml@main
  full-ci:
    needs: fast-path
    if: needs.fast-path.outputs.heavy_needed == 'true'
    # ...normal jobs here
```

Council 2026-08-05: top-3 pipeline speed win (a) — lockfile-only fast-path. Target: PR-open → live-on-staging < 5 min for the common minor/patch dep bump.
