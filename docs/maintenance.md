# Maintenance

This repository distributes the Photoshop preset consumed by ucli 1.1.0 or later. The `ucli-tools` Python distribution contains its own snapshot of the preset. Updating this repository does not change an already published Python package.

## Updating a preset

1. Create a new version directory when upgrading the backend.
2. Update `backend/package.json` and regenerate `backend/package-lock.json`. Keep the dependency pinned; review security overrides before removing them.
3. Scan the backend's tools and update `tools.json`, `cli.yaml`, and the skill references together.
4. Update `manifest.json`, including the tool count, runtime metadata, `files`, and SHA-256 checksums for every listed file. Keep the manifest itself out of its checksum map.
5. Check that every mapped tool exists and that checksums match before installing.

The current snapshot uses `@alisaitteke/photoshop-mcp@1.7.37`, with a security override for `@hono/node-server@2.1.3`.

## Local verification

Use a short, isolated directory for ucli state and skill destinations:

```bash
UCLI_USER_HOME=/tmp/photoshop-cli-check ucli install ps --preset-dir ./presets
UCLI_USER_HOME=/tmp/photoshop-cli-check ucli doctor ps
UCLI_USER_HOME=/tmp/photoshop-cli-check ucli uninstall ps --force
```

Photoshop remains a shared application. Run image-editing examples only against a disposable document.

To distribute an updated preset through `uv tool install ucli-tools`, also update the snapshot in ucli and publish a new ucli release. Consumers of this repository can install directly with `--preset-dir`.
