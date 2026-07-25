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
