# photoshop-cli

Control Photoshop from your terminal and AI agent with `ucli ps`.

Built on [ucli](https://github.com/makabakaxy/ucli), this project provides command mappings and an agent skill for 128 Photoshop tools. The backend is [photoshop-mcp](https://github.com/alisaitteke/photoshop-mcp).

```bash
ucli ps document create --width 1920 --height 1080
ucli ps layer list
ucli ps text create --text 'Hello' --fontSize 48
```

## Why

Photoshop workflows often need several steps: create a document, edit layers, add text, and export. CLI commands make those steps easy to save, repeat, and combine in scripts. An agent can use the same commands through a skill.

The Photoshop backend exposes 128 tools. Skill-based access can avoid loading the full tool catalog into an agent's context when direct MCP registration is disabled. Actual context savings depend on the client and workflow.

| | CLI through ucli | Direct MCP |
| --- | --- | --- |
| Scripts and automation | Commands, shell loops, and exit codes | Requires an MCP client |
| Agent discovery | Skill references and `--help`; needs shell access | Native tool discovery and input schemas |
| Context usage | Load relevant skill references as needed | Depends on how the client exposes tool schemas |
| Results | ucli prints tool content as text | Protocol supports structured data, images, and other content |
| Updates | Refresh mappings when backend tools change | Discover tools directly from the backend |

Use the CLI for terminal workflows and repeatable scripts. Direct MCP fits clients with native tool interfaces and rich result rendering. Both use the same Photoshop backend; the CLI is another way to access it. See the [MCP tools specification](https://modelcontextprotocol.io/specification/2025-06-18/server/tools) for the protocol features.

## Install

Requires macOS, Python 3.11+, [uv](https://docs.astral.sh/uv/), Node.js 22, 24, or 26 with npm, and Adobe Photoshop. Open Photoshop before checking the connection.

Install the Photoshop integration shipped with ucli:

```bash
uv tool install ucli-tools && ucli install ps
```

This installs the MCP backend, CLI mappings, and skill for Codex. Select other clients with `--client codex,claude,cursor`. The preset requires no AI generation.

To install the exact preset from this repository, use your GitHub SSH access:

```bash
git clone git@github.com:makabakaxy/photoshop-cli.git
cd photoshop-cli
uv tool install ucli-tools
ucli install ps --preset-dir ./presets
```

## Quick start

```bash
# Check the Photoshop connection
ucli ps app ping

# Create a canvas and add text
ucli ps document create --width 1920 --height 1080
ucli ps text create --text 'Hello' --fontSize 48

# Inspect the document and explore export options
ucli ps layer list
ucli ps document export --help
```

Use `ucli ps --help` to explore all command groups. Your agent can use the installed `ps` skill for the same workflows.

## Manage the integration

```bash
ucli doctor ps
ucli uninstall ps --dry-run
ucli uninstall ps
```

For the preset shipped with ucli, upgrade ucli and run `ucli update ps --yes`. To use a newer preset from this repository, run `git pull --ff-only` and repeat `ucli install ps --preset-dir ./presets`. If managed files were edited, review them before adding `--force`.

Configuration is stored in `~/.agents/ucli/servers.yaml`. User notes in `users/` and local helpers in `scripts/` are preserved. Installation does not disable an existing direct MCP registration in your agent client.

## Project structure

```text
presets/ps/1.7.37/
  cli.yaml             # Command paths mapped to MCP tools
  tools.json           # Tool schemas
  skills/              # Agent skill and references
  backend/             # Pinned npm dependency and lockfile
  manifest.json        # Runtime settings and file checksums
```

ucli handles installation, command routing, backend lifecycle, and skill sync. This repository distributes the Photoshop preset. See [maintenance notes](docs/maintenance.md) for updating it.

## License

MIT. The MCP backend and Adobe Photoshop have their own licenses. Adobe Photoshop is installed separately.
