# photoshop-cli

Control Adobe Photoshop from your terminal or AI agent with `ucli ps`.

Built on [ucli](https://github.com/makabakaxy/ucli), this project provides CLI mappings and an agent skill for **128 Photoshop tools**. Create documents, edit layers and text, retouch images, run batch workflows, and export assets. It uses [photoshop-mcp](https://github.com/alisaitteke/photoshop-mcp) to control your installed Photoshop app.

## Install

Requires macOS, Python 3.11+, [uv](https://docs.astral.sh/uv/), Node.js 22, 24, or 26 with npm, and Adobe Photoshop.

```bash
uv tool install ucli-tools && ucli install ps
```

This installs the Photoshop backend, CLI mappings, and skill for Codex. Use `--client codex,claude,cursor` to install the skill for other clients too. No AI generation is needed during installation.

To install the exact preset from this repository, clone it with your GitHub SSH access:

```bash
git clone git@github.com:makabakaxy/photoshop-cli.git
cd photoshop-cli
uv tool install ucli-tools
ucli install ps --preset-dir ./presets
```

## Quick start

Open Photoshop, then create a canvas, add a title, and save both an editable PSD and a PNG:

```bash
ucli ps app ping
ucli ps document create '{"width":1920,"height":1080}'
ucli ps text create '{"document_id":0,"fontSize":72,"x":120,"y":160}' --text 'Hello Photoshop'
ucli ps document save '{"document_id":0}' --path /tmp/banner.psd --format PSD
ucli ps document export '{"document_id":0}' --path /tmp/banner.png --format PNG
```

`{"document_id":0}` targets the active document. Use `ucli ps document list '{"document_id":0}'` to find document IDs, then pass a specific ID when working with several files. Most layer commands act on the selected layer.

The examples pass numeric options as JSON to preserve their types. String options can use named flags such as `--text` and `--path`.

Explore commands with `ucli ps --help` or a command's `--help`, such as `ucli ps document export --help`.

## What you can do

### Documents and canvas

Create a canvas with a chosen size, resolution, and color mode. Open images, switch between documents, resize or crop a canvas, place images as layers, and flatten the result. Save editable PSD files or export PNG, JPEG, WebP, and AVIF copies, where supported by Photoshop.

```bash
ucli ps document open --filePath /tmp/photo.jpg
ucli ps document resize '{"document_id":0,"width":1200,"height":800}'
ucli ps document export '{"document_id":0,"quality":80}' --path /tmp/photo.webp --format WEBP
```

### Layers and smart objects

List, create, select, duplicate, rename, and delete layers. Move, scale, rotate, or fit a layer; change its opacity, blend mode, visibility, and lock state; reorder layers or apply styles. Convert layers to smart objects, edit their contents, or replace an image inside a mockup.

```bash
ucli ps layer list '{"document_id":0}'
ucli ps layer select '{"document_id":0}' --name Product
ucli ps layer move '{"document_id":0,"deltaX":100,"deltaY":0}'
ucli ps layer set opacity '{"document_id":0,"opacity":80}'
```

The example assumes a layer named `Product` exists. Select the intended layer before applying layer operations.

### Text and typography

Create point text or paragraph text boxes. Set fonts, size, position, color, alignment, tracking, and leading. Update existing text or style individual ranges within a text layer. List available fonts, install a font, and set the font on the selected text layer.

```bash
ucli ps text create '{"document_id":0,"fontSize":48,"x":100,"y":120}' --text 'Summer collection'
ucli ps text set-color '{"document_id":0,"red":255,"green":100,"blue":40}'
ucli ps text update '{"document_id":0}' --text 'Summer collection 2026'
```

### Selections, masks, and fills

Select a rectangle, ellipse, the whole canvas, or the main subject. Expand, contract, feather, invert, or save a selection. Turn a selection into a layer mask, apply or delete masks, and create clipping masks. Fill areas with solid colors, gradients, or content-aware fill.

For example, select the subject and create a mask on the active layer:

```bash
ucli ps selection subject '{"document_id":0}'
ucli ps selection feather '{"document_id":0,"pixels":2}'
ucli ps mask create '{"document_id":0}'
```

### Retouching and color

Apply Gaussian, motion, or smart blur; sharpen with Unsharp Mask; add noise or use a high-pass filter. Adjust brightness, contrast, hue, saturation, curves, vibrance, and exposure. Apply photo filters, gradient maps, and LUTs, or use automatic levels and contrast.

```bash
ucli ps adjust brightness-contrast '{"document_id":0,"brightness":10,"contrast":15}'
ucli ps adjust hue-saturation '{"document_id":0,"hue":0,"saturation":10,"lightness":0}'
ucli ps filter sharpen '{"document_id":0,"amount":80,"radius":1}'
```

### AI editing

Use generative fill for a selected area, remove objects, expand the canvas, generate an image from a prompt, upscale, or replace a sky. Available features depend on your Photoshop version and account. Neural Filters also require the backend's UXP bridge plugin.

```bash
ucli ps app capabilities
# Make a selection before generative fill
ucli ps selection rectangle '{"document_id":0,"left":100,"top":100,"right":500,"bottom":400}'
ucli ps ai fill '{"document_id":0}' --prompt 'A small ceramic vase'
```

### Batch workflows and recipes

Recipes combine several Photoshop steps into one command. The preset includes 17 recipes:

| Workflow | Commands |
| --- | --- |
| Background and cleanup | `remove-background`, `remove-distraction` |
| Portrait retouching | `enhance-portrait`, `frequency-separation`, `dodge-burn` |
| Color and compositing | `apply-color-grade`, `gradient-fade`, `sky-blend` |
| Web and social assets | `prepare-for-web`, `export-social-variants`, `split-carousel` |
| Batch production | `batch-mockup-replace`, `batch-watermark`, `csv-to-cards` |
| Document finishing | `organize-layers`, `passport-photo`, `sticker-outline` |

For example, resize and export the active document for the web:

```bash
ucli ps recipe prepare-for-web '{"document_id":0,"max_dimension_px":1600,"quality":9}' --format jpeg --path /tmp/web.jpg
```

Or apply a text watermark to images in a folder:

```bash
ucli ps recipe batch-watermark '{"document_id":0,"opacity":40}' --assets-dir /tmp/photos --text 'My Studio' --position bottom_right
```

Other recipes can replace a named smart object with each image in a folder, build cards from CSV rows, split a wide image into carousel slides, or create a passport-style photo layout. Use `ucli ps recipe --help` to explore them. Recipe JPEG quality uses a 1–12 scale; `document export` uses 0–100.

### Artboards, datasets, and automation

Create, list, and activate artboards, then export them to a folder. Import Photoshop variable datasets from XML and generate a file for each dataset. Run a saved Photoshop action or custom ExtendScript for workflows beyond the built-in commands.

Inspect the current app state, document information, and layer list. Request a base64 JPEG preview, list history states, or undo and redo edits.

## Use with an AI agent

The installed `ps` skill teaches your agent the command groups and workflows. You can ask it to:

- “Create a 1920 × 1080 banner with a title, then save a PSD and PNG.”
- “Remove the background from the active image and export a transparent PNG.”
- “Replace the product image in this mockup with each image in a folder.”
- “Export this design in Instagram post and story sizes.”

The agent needs shell access to run `ucli ps`. Photoshop must be running on the same Mac as the backend.

## Why CLI?

CLI commands are easy to repeat, save in scripts, and combine with other tools. An agent can discover commands through the skill and `--help`. Direct MCP offers native tool discovery and richer result rendering in clients that support it. Both use the same Photoshop backend; this project adds a terminal interface and a reusable skill.

## Manage the integration

```bash
ucli doctor ps
ucli uninstall ps --dry-run
ucli uninstall ps
```

For the preset shipped with ucli, upgrade ucli and run `ucli update ps --yes`. For this repository's preset, run `git pull --ff-only` and repeat `ucli install ps --preset-dir ./presets`.

ucli handles installation, command routing, backend lifecycle, and skill sync. This repository contains the Photoshop preset under `presets/ps/1.7.37/`. See [maintenance notes](docs/maintenance.md) for its structure and update steps.

## License

MIT. The MCP backend and Adobe Photoshop have their own licenses. Adobe Photoshop is installed separately.
