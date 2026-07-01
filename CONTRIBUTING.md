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

1. Update `packages/{id}/manifest.json` with the new version.
2. Package the extension directory as `{id}-{version}.zip`.
3. Create a GitHub Release.
4. Upload the zip as a release asset.
5. Compute the asset SHA-256.
6. Update `packages/{id}/manifest.json`:
   - `version`
   - `assetURL`
   - `sha256`
7. Open a PR.

## Update Rules

- Release assets are immutable.
- `registry.json` only points to package manifests.
- `assetURL` must point to a GitHub Release asset.
- `sha256` must match the release asset.
- `manifest.id` must match the registry id.
