# riven-updates

Distribution point for the Riven app/firmware update-compatibility system.
Consumed by:

- **Android app** — fetches `manifest.json` (static file, not the GitHub
  API) to check firmware/app currency advisories. Never writes to this repo.
- **Win/Mac loader app** — fetches `manifest.json` for its own currency
  check; publishes firmware binaries and its own installer here as
  [Releases](../../releases).

This repo holds **no source code** — only the manifest and release
binaries. Source stays in its existing (private) repo.

## manifest.json

Fetched directly as a raw file, e.g.:
```
https://raw.githubusercontent.com/YOUR_ORG/riven-updates/main/manifest.json
```
Not via the GitHub REST API — deliberately, to avoid any API rate-limit
exposure as device count grows. See `schemaVersion` in the file itself;
any consumer that doesn't recognize the schema version must fail open
(ignore the advisory, don't crash) rather than guess at the shape.

Field meaning and full design context: see `application_update-plan.md`
in the source repo.

## Publishing a manifest update

Treat this as a reviewed step, not an automated one — a wrong claim here
either falsely advertises firmware support the app doesn't have, or fails
to surface an update that fixes a real gap. Before merging a change to
`manifest.json`:

1. The release it describes (APK and/or firmware binary) must already
   exist under [Releases](../../releases).
2. `sha256` must match the actual published artifact
   (`shasum -a 256 <file>` / `Get-FileHash -Algorithm SHA256 <file>`).
3. `knownSettingKeys` reflects what that specific released version
   actually understands — not what's planned or in progress.

## Releases

Binaries (APKs, firmware images, loader installers) are attached to
[GitHub Releases](../../releases), not committed to the repo tree.
