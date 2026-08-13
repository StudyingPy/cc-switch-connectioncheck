# Fork updater release guide

This fork checks for updates from:

`https://github.com/StudyingPy/cc-switch-connectioncheck/releases/latest/download/latest.json`

The updater public key is committed in `src-tauri/tauri.conf.json`. The matching
private key is stored locally at `.secrets/tauri-updater.key` and is ignored by
Git. Back it up securely: losing it means future builds cannot update existing
installations automatically.

## One-time GitHub setup

From PowerShell in the repository root, upload the private key to the fork:

```powershell
Get-Content -LiteralPath '.secrets/tauri-updater.key' -Raw -Encoding UTF8 |
  gh secret set TAURI_SIGNING_PRIVATE_KEY --repo StudyingPy/cc-switch-connectioncheck
```

This key has no password, so `TAURI_SIGNING_PRIVATE_KEY_PASSWORD` is not needed.

## Publish a Windows updater release

1. Update the version consistently in `package.json`, `src-tauri/tauri.conf.json`,
   and `src-tauri/Cargo.toml`.
2. Commit and push the version change.
3. Create and push the matching `v*` tag.

```powershell
git tag v3.19.2
git push origin v3.19.2
```

`Release (Windows)` then builds the signed MSI, publishes a normal GitHub
Release, and uploads `latest.json`. Normal releases are required because
GitHub's `/releases/latest/` URL does not select prereleases.

The full multi-platform `Release` workflow is manual-only in this fork because
it additionally requires Apple signing and notarization secrets. Run it from a
`v*` tag only after configuring all platform credentials.

## Existing installations

Builds that still contain the upstream public key cannot accept updates signed
by this fork. Users must manually install one migration build containing this
fork's endpoint and public key. Automatic updates work from that build onward.
