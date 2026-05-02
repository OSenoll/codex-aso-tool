# CODEX.md

This repository contains a Codex-compatible skill named `codex-aso-tool`.

## Structure

- `SKILL.md` defines the Codex workflow and trigger metadata.
- `agents/openai.yaml` provides Codex/OpenAI UI metadata.
- `compose.py` creates deterministic 1290x2796 App Store screenshot scaffolds.
- `generate_frame.py` regenerates `assets/device_frame.png`.
- `showcase.py` creates a side-by-side preview from approved screenshots.
- `assets/device_frame.png` is the bundled iPhone frame template used by `compose.py`.

## Codex State

Codex does not use Claude Code memory. The skill persists app-specific state in the target app repository:

```text
.codex/aso-appstore-screenshots/
  benefits.md
  screenshot-analysis.md
  pairings.md
  generation.md
```

## Notes

The deterministic generation path only requires Python and Pillow. AI enhancement is optional and depends on which image editing/generation tools are available in the active Codex session.
