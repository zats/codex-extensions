# Codex Extensions

Community extension registry for Bibliotheca-managed Codex desktop extensions.

## Structure

```text
registry.json
packages/
  accounts/
    manifest.json
    src/
  colors/
    manifest.json
    src/
```

`registry.json` is the small discovery and search index. Each package manifest lives at `packages/{id}/manifest.json` and stores runtime, compatibility, and release metadata. Packaged extension assets are published through GitHub Releases.
