# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A Codex-first skill (`codex-aso-tool`) that guides users through creating high-converting App Store screenshots. The repository remains compatible with Claude Code conventions where practical.

## Architecture

Four files + one asset make up the skill:

- **SKILL.md** — The skill prompt. Defines a multi-phase workflow: Benefit Discovery → Screenshot Pairing → Generation. Codex persists state in `.codex/aso-appstore-screenshots/` inside the target app repository. Generation first creates a deterministic scaffold via compose.py, then optionally uses available image tooling for enhancement.
- **compose.py** — A standalone Python compositing script (Pillow-based) that deterministically renders App Store screenshots. Takes a background hex colour, action verb, benefit descriptor, and simulator screenshot path, then produces a pixel-perfect 1290×2796 PNG with headline text, device frame template, and the screenshot composited inside. The verb text auto-sizes to fit the canvas width.
- **generate_frame.py** — Generates the device frame template PNG (`assets/device_frame.png`). Run once to create or update the template. The template is a 1290×2796 RGBA PNG with a black iPhone body, transparent screen cutout, Dynamic Island, and side buttons.
- **showcase.py** — Generates a showcase image showing up to 3 final screenshots side-by-side with an optional GitHub link at the bottom. Used as the final step after all screenshots are approved.
- **assets/device_frame.png** — Pre-rendered iPhone device frame template used by compose.py. Using a template instead of drawing the frame at compose time ensures pixel-perfect consistency across all generated screenshots.

## Running compose.py

```bash
# Requires: pip install Pillow
# Requires: SF Pro Display Black font at /Library/Fonts/SF-Pro-Display-Black.otf

python3 compose.py \
  --bg "#E31837" \
  --verb "TRACK" \
  --desc "TRADING CARD PRICES" \
  --screenshot path/to/simulator.png \
  --output output.png \
  --accent  # optional: adds dark arc behind device
```

## Key Design Decisions

- **Two-stage generation**: compose.py creates a deterministic scaffold first (text + frame + screenshot), then optional image tooling can enhance it. This avoids the inconsistencies of generating from scratch.
- **compose.py outputs exact App Store Connect dimensions** (1290×2796 for iPhone 6.7") — no post-processing crop needed.
- **Device frame is a template image** (`assets/device_frame.png`) — not drawn at compose time. Regenerate with `python3 generate_frame.py` if the frame design needs updating.
- **Verb text auto-sizes** — shrinks from 172px down to 100px to fit multi-word verbs (e.g. "TURN YOURSELF") within the canvas width.
- **SKILL.md always generates 3 versions in parallel** for each benefit so the user can pick the best one.
- **The crop/resize step in SKILL.md is mandatory** after image generation or editing when the raw output is not the correct dimensions for App Store Connect.
- **State is central to the workflow** — benefits, screenshot assessments, pairings, brand colour, and generation state are all persisted so users can resume across conversations.
