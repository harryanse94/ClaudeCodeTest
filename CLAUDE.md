# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Git Workflow

After completing any meaningful unit of work, commit and push to GitHub so progress is never lost:

```
git add <specific files>
git commit -m "short, descriptive message"
git push
```

Commit frequently — after each feature, fix, or significant change. Never batch unrelated changes into one commit. Always push immediately after committing.

## Running the Games

No build step required. Open any HTML file directly in a browser:

```
open shooter.html
open tictactoe.html
```

## Project Contents

- **`shooter.html`** — "Fried Chicken Run", a complete top-down browser shooter (~768 lines)
- **`tictactoe.html`** — Two-player Tic Tac Toe with score tracking

## Shooter Architecture (`shooter.html`)

Single self-contained file. All logic is inside one `<script>` tag with no dependencies.

**Rendering:** HTML5 Canvas at logical resolution 320×240, scaled 3× to 960×720 via `ctx.setTransform(SCALE,0,0,SCALE,0,0)` each frame. `image-rendering: auto` for smooth cartoon look. Sprites are drawn programmatically with Canvas 2D API (no external images).

**Game loop:** `requestAnimationFrame` with delta-time (`dt = Math.min(ts-lastT, 50)`). State machine: `MENU → PLAYING → LEVEL_COMPLETE → PLAYING → GAME_OVER → MENU`. State stored in `gs` string.

**Key globals:**
- `P` — player object (position, velocity, hp, angle, fire state)
- `enemies`, `pBullets`, `eBullets`, `particles`, `powerups` — entity arrays
- `effects` — `{SPEED, RAPID, SHIELD, DOUBLE}` object storing expiry timestamps (ms). Active if `effects.X > Date.now()`.
- `bgC`/`bgX` — offscreen canvas for pre-rendered tiled background (rebuilt on level change via `buildBg(shade)`)

**Enemy types** defined in `ET` constant: GRUNT, RUNNER, TANK, SHOOTER, BOSS — each has hp, speed, size, score, spawn weight, and optional shooting behaviour.

**Power-up types** defined in `PUTYPE`: HEAL (instant), SPEED, RAPID, SHIELD, DOUBLE (all timed). Drop on enemy death; also spawn periodically via `puTimer`. Picked up by walking over (radius 10px logical).

**Mouse coordinates** convert via `(e.clientX - r.left) / r.width * W` to account for CSS scaling.

**Canvas gradients** in `drawPlayer()` are created after `ctx.translate`/`ctx.rotate`, so their coordinates are in local sprite space.
