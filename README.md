# Codex ASO Tool

A Codex-first skill for creating high-converting App Store screenshots for iOS apps.

It analyzes your app, identifies the strongest download benefits, reviews simulator screenshots, pairs each benefit with the best screen, and produces App Store-ready screenshot assets.

## What It Does

1. **Benefit Discovery** - reads the app codebase and drafts 3-5 ASO benefit headlines.
2. **Screenshot Review** - rates simulator screenshots as `Great`, `Usable`, or `Retake`.
3. **Screenshot Pairing** - maps each benefit to the strongest screenshot.
4. **Scaffold Generation** - creates exact App Store-sized screenshot layouts with `compose.py`.
5. **Optional AI Enhancement** - supports OpenAI, Gemini/Nano Banana, ChatGPT web, Gemini web, or scaffold-only workflows.
6. **Final Export** - crops/resizes approved images and saves final App Store-ready files.

## Quick Start

Install the skill locally:

```bash
mkdir -p ~/.codex/skills
cp -R /path/to/codex-aso-tool ~/.codex/skills/codex-aso-tool
```

From your iOS app repository, invoke:

```text
$codex-aso-tool
```

Codex saves progress in the target app repository:

```text
.codex/aso-appstore-screenshots/
  benefits.md
  screenshot-analysis.md
  pairings.md
  generation.md
```

## Enhancement Modes

The tool is provider-agnostic. It always starts with deterministic scaffolds, then chooses the best enhancement route available.

Priority order:

1. **Built-in Codex image editing tool** - if the active Codex session exposes one.
2. **OpenAI GPT Image API** - when `OPENAI_API_KEY` is available and API automation is wanted.
3. **Gemini/Nano Banana API or MCP** - when `GEMINI_API_KEY` or a usable Gemini MCP image tool is available.
4. **ChatGPT web manual mode** - when you have ChatGPT image access but no OpenAI API key.
5. **Gemini web manual mode** - when you have Gemini Pro/Nano Banana web access but no Gemini API key.
6. **Scaffold-only mode** - when no external AI enhancement is wanted.

Web subscriptions are not API keys. ChatGPT Plus/Pro and Gemini Pro can still be useful through manual web mode, but they do not automatically give Codex API access.

## Manual Web Mode

Use this when you only have ChatGPT or Gemini in the browser.

For each screenshot, Codex generates:

```text
screenshots/01-benefit-slug/
  scaffold.png
  chatgpt-prompt.md
  gemini-prompt.md
```

Then you:

1. Open ChatGPT or Gemini/Nano Banana in the browser.
2. Upload `scaffold.png`.
3. Paste the matching prompt file.
4. Generate 3 variants.
5. Save them as:

```text
screenshots/01-benefit-slug/v1.png
screenshots/01-benefit-slug/v2.png
screenshots/01-benefit-slug/v3.png
```

Codex then handles review, crop/resize, final selection, and export.

## API/MCP Automation

Automation is optional.

OpenAI:

```bash
export OPENAI_API_KEY="..."
```

Gemini:

```bash
export GEMINI_API_KEY="..."
```

Gemini MCP can also be used in environments that expose a usable image editing tool. Most MCP setups still require API-backed credentials.

## Requirements

Install Pillow:

```bash
pip install Pillow
```

The scaffold generator expects SF Pro Display Black on macOS:

```text
/Library/Fonts/SF-Pro-Display-Black.otf
```

Install it from Apple's developer fonts if needed.

## Output

Generated files are written to the target app repository:

```text
screenshots/
  01-benefit-slug/
    scaffold.png
    chatgpt-prompt.md
    gemini-prompt.md
    v1.png
    v2.png
    v3.png
    v1-resized.png
  final/
    01-benefit-slug.png
    02-benefit-slug.png
  showcase.png
```

`screenshots/final/` contains the approved App Store-ready screenshots. Default target size is iPhone 6.7": `1290 x 2796`.

## How It Works

The skill uses a two-stage pipeline:

1. `compose.py` creates a deterministic scaffold with headline text, a device frame, a solid brand-color background, and the selected simulator screenshot.
2. An optional image model or manual web workflow enhances the scaffold into a more polished marketing asset.

This keeps layout, text, and App Store dimensions controlled while still allowing AI visual polish.

## Files

| File | Purpose |
| --- | --- |
| `SKILL.md` | Codex skill workflow and trigger metadata |
| `agents/openai.yaml` | Codex/OpenAI UI metadata |
| `compose.py` | Deterministic App Store screenshot scaffold generator |
| `generate_frame.py` | Regenerates the bundled iPhone frame template |
| `showcase.py` | Creates a side-by-side showcase image |
| `assets/device_frame.png` | Pre-rendered iPhone frame template |

## Claude Code Compatibility

This is Codex-first, but the workflow can be adapted for Claude Code. A Claude setup may use a Gemini MCP server such as `@houtini/gemini-mcp` when configured separately.

## License

MIT
