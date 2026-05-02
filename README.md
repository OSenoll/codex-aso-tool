# Codex ASO Tool

A Codex-first skill that helps generate high-converting App Store screenshots for your iOS app. It analyzes your codebase, identifies core benefits, reviews simulator screenshots, and creates App Store-ready screenshot images.

## What It Does

1. **Benefit Discovery** — Analyzes your app's codebase to identify the 3-5 core benefits that drive downloads
2. **Screenshot Pairing** — Reviews your simulator screenshots, rates them, and pairs each with the best benefit
3. **Generation** — Creates polished App Store screenshots using deterministic scaffolding (`compose.py`) with optional provider-based AI enhancement
4. **Showcase** — Generates a preview image with all screenshots side-by-side

## Installation

### Codex

```bash
mkdir -p ~/.codex/skills
cp -R /path/to/codex-aso-tool ~/.codex/skills/codex-aso-tool
```

Invoke it with:

```text
$codex-aso-tool
```

Codex progress is saved in the target app repository under:

```text
.codex/aso-appstore-screenshots/
```

### Claude Code Compatibility

The skill can still be adapted for Claude Code usage. If publishing a Claude-compatible package too, install it with the target repository URL:

```bash
claude install-skill github.com/YOUR_USERNAME/codex-aso-tool
```

### Python dependencies

```bash
pip install Pillow
```

### Font requirement

The skill uses **SF Pro Display Black** for headline text. On macOS, install it from [Apple's developer fonts](https://developer.apple.com/fonts/). The expected path is:

```
/Library/Fonts/SF-Pro-Display-Black.otf
```

### Optional AI Enhancement

Automated enhancement requires an image editing tool or API-backed provider access. Web subscriptions are useful for manual mode, but they are not the same as API keys.

Supported enhancement routes:

- Built-in/local image editing tool when available in the active Codex session
- OpenAI GPT Image API with `OPENAI_API_KEY`
- Gemini/Nano Banana API or MCP with API-backed access
- ChatGPT web manual mode
- Gemini web manual mode
- Scaffold-only mode

For Claude Code, the original workflow can use [@houtini/gemini-mcp](https://www.npmjs.com/package/@houtini/gemini-mcp) for Gemini/Nano Banana enhancement:

```bash
npm install -g @houtini/gemini-mcp
```

Then add it to your Claude Code MCP config (`~/.claude/settings.json` or project `.mcp.json`).

For Codex, the skill uses whatever image generation/editing capability is available in the current session. If none is available, it still produces exact App Store-sized scaffold screenshots using `compose.py`.

If you only have ChatGPT or Gemini Pro in the browser, use manual web mode:

1. Codex generates `scaffold.png`, `chatgpt-prompt.md`, and `gemini-prompt.md`.
2. You upload the scaffold to ChatGPT or Gemini/Nano Banana in the browser.
3. You paste the matching prompt and generate 3 variants.
4. You save them as `v1.png`, `v2.png`, and `v3.png`.
5. Codex handles crop/resize, review, and final export.

## Usage

From within your app's project directory, invoke the skill in Codex:

```text
$codex-aso-tool
```

The skill will guide you through each phase interactively. Codex stores progress in `.codex/aso-appstore-screenshots/` inside the target app project.

## How It Works

### Scaffold → Enhance Pipeline

Rather than generating screenshots from scratch (which produces inconsistent results), the skill uses a two-stage approach:

1. **compose.py** creates a deterministic scaffold with exact text positioning, device frame, and your simulator screenshot composited inside
2. An optional image-editing model enhances the scaffold by adding a photorealistic device frame, breakout elements, and visual polish. Without API/MCP access, the skill supports ChatGPT and Gemini web handoff prompts.

This ensures consistent layout across all screenshots while letting AI handle the creative enhancement.

### Output

Screenshots are saved to a `screenshots/` directory in your project:

```
screenshots/
  01-benefit-slug/          ← working versions
    scaffold.png            ← deterministic compose.py output
    v1.png, v2.png, v3.png  ← AI-enhanced versions
    v1-resized.png, ...     ← cropped to App Store dimensions
  final/                    ← approved screenshots, ready to upload
    01-benefit-slug.png
    02-benefit-slug.png
  showcase.png              ← preview image with all screenshots
```

The `final/` folder contains App Store-ready screenshots at exact Apple dimensions (default: 1290×2796px for iPhone 6.7").

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | The skill prompt — defines the multi-phase Codex workflow |
| `agents/openai.yaml` | Codex/OpenAI UI metadata |
| `compose.py` | Deterministic scaffold generator (Pillow-based) |
| `generate_frame.py` | Generates the device frame template |
| `showcase.py` | Generates the side-by-side showcase image |
| `assets/device_frame.png` | Pre-rendered iPhone device frame template |

## License

MIT
