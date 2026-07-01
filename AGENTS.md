# Codex Extension Registry Agent Guide

This repo is the public extension registry. Keep changes small, release assets immutable, and preserve user data across updates.

## Repository Shape

- `registry.json` is the discovery index. Each entry contains only `id`, `description`, and latest `version`.
- `packages/{id}/manifest.json` is the package source of truth for metadata, compatibility, release URL, checksum, and entrypoints.
- `packages/{id}/src/` contains extension runtime code.
- GitHub Releases host immutable extension zip assets.

## Create an Extension

1. Pick a stable lowercase id such as `colors` or `accounts`.
2. Create `packages/{id}/manifest.json`.
3. Create `packages/{id}/src/`.
4. Add entrypoints only when used:
   - `main`: Electron main-process integration.
   - `preload`: bridge APIs exposed to the renderer.
   - `renderer`: DOM/UI integration in Codex.
5. Add a `registry.json` entry with:
   - `id`
   - `description`
   - `version`
6. Start at `1.0.0` unless the extension is explicitly experimental.
7. Use commit message: `Add {id} extension`.

## Update an Extension

1. Edit only files under `packages/{id}` unless the registry index must change.
2. Preserve user data. Updating must not delete extension-owned data files. Only uninstall should remove user data.
3. Bump `packages/{id}/manifest.json` `version` every time runtime behavior changes.
4. Bump `registry.json` entry `version` to the same value.
5. Use semver:
   - Patch for fixes.
   - Minor for new compatible behavior.
   - Major for intentional breaking behavior.
6. Use commit message: `Publish {id} {version}`.

## Publish a Release

Release assets are immutable. Never replace an uploaded zip. Publish a new version instead.

1. Build from a staged temp copy, not directly from the repo.
2. The manifest inside the zip must omit registry-only fields:
   - `assetURL`
   - `sha256`
3. Create the zip with macOS metadata disabled:

   ```sh
   COPYFILE_DISABLE=1 zip -X -r {id}-{version}.zip manifest.json src
   ```

4. Create GitHub Release/tag:

   ```sh
   gh release create {id}-v{version} {id}-{version}.zip --title {id}-v{version} --notes "{short release note}"
   ```

5. Download the uploaded asset and compute SHA-256 from the downloaded file:

   ```sh
   tmpdir=$(mktemp -d)
   gh release download {id}-v{version} --pattern {id}-{version}.zip --dir "$tmpdir" --clobber
   shasum -a 256 "$tmpdir/{id}-{version}.zip"
   trash "$tmpdir"
   ```

   Copy only the first field from `shasum`; that is the `sha256` value.
6. Update `packages/{id}/manifest.json`:
   - `version`
   - `assetURL`
   - `sha256`
7. Update `registry.json` latest version.
8. Validate JSON and JavaScript:

   ```sh
   node -e "JSON.parse(require('fs').readFileSync('registry.json','utf8'))"
   node --check packages/{id}/src/main.js
   node --check packages/{id}/src/preload.js
   node --check packages/{id}/src/renderer.js
   ```

9. Commit with `Publish {id} {version}`.
10. Push `main`.

## Local Testing

- Patch `/Users/zats/.codex/extensions/{id}/src/` only for immediate local testing.
- Do not manually edit `/Users/zats/.codex/extensions/{id}/manifest.json` to fake an installed version.
- After publishing, expect GitHub raw CDN to lag. Verify the source of truth with:

  ```sh
  gh api repos/zats/codex-extensions/contents/registry.json --jq '.content' | base64 --decode
  ```

## Safety Rules

- Do not add fallback behavior unless requested.
- Do not preserve compatibility with old registry shapes unless requested.
- Do not put large metadata in `registry.json`.
- Do not commit local test patches under `/Users/zats/.codex/extensions`.
- Keep release zips free of `._*`, `.DS_Store`, and unrelated files.
