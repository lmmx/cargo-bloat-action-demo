# cargo-bloat Action demo

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
