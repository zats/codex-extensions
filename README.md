# Codex Extensions

Community extension registry for Bibliotheca-managed Codex desktop extensions.

## Structure

```text
registry.json
extensions/
  accounts.json
  colors.json
packages/
  accounts/
    manifest.json
    src/
  colors/
    manifest.json
    src/
```

`registry.json` is the small discovery index. Each `extensions/{id}.json` stores install and update metadata for one extension. Packaged extension assets are published through GitHub Releases.
