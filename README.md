# cargo-bloat Action demo

GitHub Actions workflow that will show the % size increase via cargo bloat for a PR.

The baseline has the default `cargo init` hello world binary app, clocking in at 472KB.

```sh
$ du -h target/release/cargo-bloat-action-demo
472K    target/release/cargo-bloat-action-demo
```
