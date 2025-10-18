# cargo-bloat GitHub Actions Demo

Shows binary size changes in PRs using cargo bloat.

- [Example PR](https://github.com/lmmx/cargo-bloat-action-demo/pull/1)
- [Workflow source](https://github.com/lmmx/cargo-bloat-action-demo/blob/master/.github/workflows/bloat.yml)

![](https://github.com/user-attachments/assets/69c245ce-f529-4ae0-8f98-3d87c0d9a2dd)

## How it works

Builds both branches with separate `CARGO_TARGET_DIR` to avoid full rebuilds:
- Base branch → `target-base/`
- PR branch → `target/` (default)

Shows size change as inline status for same-repo PRs, plus detailed analysis in check summary.

## Features

- mold linker for faster builds
- rust-cache for dependency caching
- Fork PRs see analysis in check summary (inline status skipped due to GitHub permissions)

If you primarily accept PRs from forks, delete the "Update commit status" step from the workflow to avoid the skipped check.

## Setup

Uses [taiki-e/install-action](https://github.com/taiki-e/install-action) for cargo-bloat. Requires `contents: read` and `statuses: write` permissions.
