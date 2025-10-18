# cargo-bloat GitHub Actions Demo

GitHub Actions workflow that will show the % size increase via cargo bloat for a PR.

- See [example PR](https://github.com/lmmx/cargo-bloat-action-demo/pull/1) PR
- Read the GitHub Actions [workflow YAML source](https://github.com/lmmx/cargo-bloat-action-demo/blob/master/.github/workflows/bloat.yml)

The baseline has the default `cargo init` hello world binary app, clocking in at 472KB.

```sh
$ du -h target/release/cargo-bloat-action-demo
472K    target/release/cargo-bloat-action-demo
```

When a PR is opened or otherwise pushed to, the check runs and the value is displayed via the Check
status line:

![](https://github.com/user-attachments/assets/69c245ce-f529-4ae0-8f98-3d87c0d9a2dd)

## How it works

The workflow compares binary sizes between the PR branch and the base branch (e.g., `master`/`main`). To avoid slow full rebuilds, it sets Cargo's `CARGO_TARGET_DIR` environment variable:

1. **Base branch build**: Builds into `target-base/` directory
2. **PR branch build**: Builds into `target/` directory (default)

This allows both builds to share compiled dependencies from Cargo's cache while keeping their final artifacts separate. It takes approximately 1.5× build time rather than 2× to build both.

The workflow then extracts the `.text` section size from both `cargo bloat` outputs, calculates the percentage change, and displays it in two ways:

1. **Main check** (`Cargo Bloat Analysis / bloat-check`): Always runs, contains the full analysis in the step summary
2. **Status check** (`Binary Size Change`): Shows the percentage inline in the checks list (only for same-repo PRs)

### Performance optimisations

- **[mold linker](https://github.com/rui314/mold)**: Speeds up the linking phase during compilation
- **[Swatinem/rust-cache](https://github.com/Swatinem/rust-cache)**: Caches Rust dependencies between workflow runs
- **Separate target directories**: Avoids rebuilding everything when comparing branches

These optimisations should significantly reduce your build times, especially on subsequent runs where dependencies are already cached.
Note that warnings on the check details page about the cache being found are normal.

## Details

- Uses [taiki-e/install-action](https://github.com/taiki-e/install-action) to install `cargo-bloat`
- Separate `CARGO_TARGET_DIR` for each branch to avoid conflicts
- Parses `cargo bloat` output to extract binary sizes
- Creates a GitHub commit status via the API for inline PR display (no secret storage required, only
  the `contents: read` and `statuses: write` workflow permissions)

### Fork compatibility

This workflow creates **two check items** in the PR checks list:

1. **`Cargo Bloat Analysis / bloat-check`** - Always runs for all PRs
2. **`Binary Size Change`** - Only appears for PRs from the same repository (not forks)

For PRs from forks, the `Binary Size Change` check will be skipped (appearing as "Expected — Waiting for status to be reported").
This is due to GitHub's security model - fork PRs run with read-only permissions to prevent malicious code from writing to your repository.

**If your repository primarily accepts PRs from forks**, you can remove the `Update commit status` step entirely to avoid the extra skipped check.
Fork contributors will still see the complete binary size comparison in the main check's step summary and notice annotations - the inline status is purely cosmetic.
