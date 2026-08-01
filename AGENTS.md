# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

Personal [Scoop](https://scoop.sh) bucket. Currently holds a single app manifest, `bucket/eden.json`, for the [Eden](https://git.eden-emu.dev/eden-emu/eden) Nintendo Switch emulator. `TODO.md` states the working goal in plain language — keep it in sync with reality or delete it once done.

## Manifest conventions (`bucket/*.json`)

Each file is a standard [Scoop app manifest](https://github.com/ScoopInstaller/Scoop/wiki/App-Manifests). `bucket/eden.json` is the reference example:

- `architecture.64bit.url` / `hash` — direct download link and its hash (`hash` is currently empty and needs filling in per release).
- `checkver` — polls `https://git.eden-emu.dev/eden-emu/eden/releases` (a Gitea instance, not GitHub) with regex `v([\d.]+)` to find the latest version.
- `autoupdate.architecture.64bit.url` — currently a placeholder (`https://example.com/...`); needs to be templated against Eden's real release asset naming, e.g. `https://stable.eden-emu.dev/v$version/Eden-Windows-v$version-amd64-clang-pgo.zip`.
- Per `TODO.md`, releases must be filtered to the **amd64/x86_64 "v3" clang-pgo** Windows build specifically — check actual asset names on each release before wiring up `autoupdate`, since PGO/non-PGO and v2/v3 variants can coexist under similar filenames.

There is no `bin/checkver.ps1` / `bin/auto-pr.ps1` (the scripts the official Scoop bucket template usually ships) — updates to `hash`/`autoupdate` are manual for now.

## Commands

No build step; there's no test runner or CI workflow configured for the bucket itself (`.github/workflows/` doesn't exist).

- Validate manifest JSON: `jq empty bucket/eden.json` (or any of the JSON-schema pre-commit hooks below).
- Test a manifest locally: `scoop install "bucket/eden.json"`, or add this repo as a local bucket first — `scoop bucket add ven0m0 <path-to-this-repo>` then `scoop install ven0m0/eden`.
- Pre-commit hooks are defined in `.github/.pre-commit-config.yaml`; run with `pre-commit run --all-files`. Only a subset applies to this repo's actual content — `check-json`, `check-yaml`, `trailing-whitespace`, `end-of-file-fixer`, and the `renovatebot`/`check-github-actions` validators. The rest (`ruff`, `basedpyright -p Scripts/snapmem`, the PSScriptAnalyzer hook targeting `Scripts/**/*.ps1`) reference paths that don't exist in this repo — they were carried over from an unrelated multi-language dotfiles project and are effectively no-ops here.

## Notable gaps

- No root `.gitignore` (only `.github/.gitignore`, `.codegraph/.gitignore`, `.serena/.gitignore` — these only govern their own subdirectories, not the repo root).
- No `README.md`.
- `renovate.json` and `dependabot.yml` configure ecosystems (Python/uv, npm, cargo, PowerShell) this repo doesn't use — also inherited boilerplate, not a signal that those toolchains belong here.
