# Project memory

## Upstream sync and release

- `origin` is `StudyingPy/cc-switch-connectioncheck`; `upstream` is `farion1231/cc-switch`.
- Keep fork-specific updater settings in `src-tauri/tauri.conf.json`, fork release/source URLs, `.github/workflows/release-win.yml`, manual-only `.github/workflows/release.yml`, `pnpm-workspace.yaml` `allowBuilds`, and the real streaming health checks under `src-tauri/src/services/stream_check.rs`.
- When an upstream release tag has later commits on `upstream/main`, publish the tag commit first, then merge the later commits into `main`. Do not point the release tag at the post-release merge.
- Fetching all upstream tags may fail because fork release tags intentionally point at fork merge commits. Fetch `upstream main` and query the needed upstream tag with `git ls-remote`; replace only the local tag before publishing the fork tag.
- Full Vitest can have upstream concurrency/timing flakes; compare against a clean upstream worktree and rerun failing files alone. Windows Rust tests may be affected by the running app occupying port 15721 or missing symlink privilege; GitHub's clean Windows runner is the authoritative confirmation.
- Networked CLI commands must use the explicit per-command proxy `http://127.0.0.1:2235`; do not set persistent global proxy environment variables.
- On this workstation, invoke the WinGet pnpm executable explicitly when the generic `pnpm` launcher hangs or leaves nested processes. Match `package.json` (`pnpm@10.12.3`), set `CI=true` for non-interactive reinstall, and pass the per-command proxy; do not change the global pnpm version.
- On Windows, a full parallel `cargo test` may exhaust the page file while mmap'ing `libcc_switch_lib.rlib` (`os error 1455`) and trigger cascading rustc ICEs. Retry with `CARGO_INCREMENTAL=0`, `-j 1`, and `--test-threads=1`; symlink tests still require Developer Mode/elevation, and the running app may occupy port 15721.
- Pi is an additive app and `get_adapter(AppType::Pi)` returns `None`. The fork's real-LLM stream check must route Pi through the root-level `baseUrl`/`apiKey`/`api` protocol dispatcher (like OpenClaw), not through a proxy adapter or upstream's reachability-only probe.
