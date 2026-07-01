# Contributing

## Create an Extension

1. Create `packages/{id}/manifest.json`.
2. Put extension code under `packages/{id}/src`.
3. Add the extension to `registry.json`.
4. Open a PR.

## Manifest

Required fields:

- `id`
- `name`
- `version`
- `codexVersionRange`
- `repo`
- `assetURL`
- `sha256`
- `entrypoints`

Example:

```json
{
  "id": "colors",
  "name": "Colors",
  "version": "1.0.0",
  "codexVersionRange": ">=26.623.42026 <27.0.0",
  "repo": "zats/codex-extensions",
  "assetURL": "https://github.com/zats/codex-extensions/releases/download/colors-v1.0.0/colors-1.0.0.zip",
  "sha256": "...",
  "entrypoints": {
    "main": "src/main.js",
    "preload": "src/preload.js",
    "renderer": "src/renderer.js"
  }
}
```

## Publish a Version

1. Edit files under `packages/{id}`.
2. Bump `packages/{id}/manifest.json` `version`.
3. Build the release zip from a staged copy of `packages/{id}`. The manifest inside the zip should include runtime fields only; omit registry fields such as `assetURL` and `sha256`.

   ```sh
   COPYFILE_DISABLE=1 zip -X -r {id}-{version}.zip manifest.json src
   ```

4. Create the GitHub Release/tag `{id}-v{version}`.
5. Upload `{id}-{version}.zip`.
6. Compute SHA-256 for the exact uploaded zip:

   ```sh
   shasum -a 256 {id}-{version}.zip
   ```

7. Update `packages/{id}/manifest.json`:
   - `version`
   - `assetURL`
   - `sha256`
8. Update `registry.json` entry `version`.
9. Open a PR.

`COPYFILE_DISABLE=1` prevents macOS `._*` files from being added to the archive.
`zip -X` prevents extra filesystem metadata from being added to the archive.
Release assets are immutable; publish a new version instead of replacing an asset.

## Update Rules

- Release assets are immutable.
- `registry.json` entries only include `id`, `description`, and `version`.
- Package manifests live at `packages/{id}/manifest.json`.
- `assetURL` must point to a GitHub Release asset.
- `sha256` must match the release asset.
- `manifest.id` must match the registry id.
