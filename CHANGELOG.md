# Changelog

All notable changes to `Onevision-io/renovate-config` shared Renovate preset.

## v1 — 2026-08-05: queue smoothing, devDep fast-path, lockfile fast-path

Initial tagged release. Consumers currently pin via `github>Onevision-io/renovate-config` (floating on `main`). A follow-up will migrate consumer repos to `github>Onevision-io/renovate-config#v1` once `v1` has soaked for a few days.

Highlights of the preset at this cut:
- Queue smoothing (concurrency + rate-limits) to prevent PR storms
- devDependency fast-path (auto-merge on green CI)
- Lockfile-only update fast-path (auto-merge on green CI)

## Pin migration (planned)

After v1 soak (target ~1 week), sweep consumers to switch:
- `"github>Onevision-io/renovate-config"` → `"github>Onevision-io/renovate-config#v1"`

Fleet-wide edit; not done in the v1 tag PR.
