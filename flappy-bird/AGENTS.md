# AGENTS.md

## Project

Vanilla JS + HTML5 Canvas Flappy Bird clone. **Zero dependencies, zero build step, ES5 only.** No framework, no modules, no tooling.

See [README.md](README.md) for full docs.

## Commands

There is no test runner, linter, or build system in this project — do not add one.

| Command | Purpose |
|---|---|
| `python3 -m http.server 8000` | Serve the repo root, then open `http://localhost:8000` |
| `node --check flappyBird.js` | Syntax check the game file (only validation available) |

> **Never open `index.html` via `file://`** — images/audio load by relative path and will fail without an HTTP server.

## Architecture

Single-file game (`flappyBird.js`) driven by a `requestAnimationFrame` loop:

- **State machine**: `welcome` → `countdown` → `playing` → (`gameOver` flag) → restart
- **Delta-time scaled physics**: `dt` from rAF timestamps; gravity/flap are per-second, pipe scroll per-second
- **Pipes**: spawned from `pipe[i].x <= cvs.width - nextGap`; the newest pipe in the array triggers the next spawn
- **Input**: `keydown` (filtered to `Space` / `ArrowUp`), `pointerdown` (left button only); right-click/copy/drag are `preventDefault`ed
- **Game over**: `endGame()` sets `gameOver`, resets `gameOverT`; restart is gated by `GAME_OVER_DELAY` (1 s) to prevent accidental restarts

## Tunable game constants (top of `flappyBird.js`)

| Constant | Default | Meaning |
|---|---|---|
| `gap` | `85` | Vertical hole between a pipe pair (px) |
| `flapPower` | `32` | Upward boost per flap (px) |
| `gravity` | `1.5` | Falling acceleration |
| `speed` | `60` | Pipe scroll speed (px/s) |
| `pipeGapMin` / `pipeGapMax` | `140` / `250` | Horizontal distance between pipe pairs (px, random) |
| `holeDeltaMax` | `120` | Max vertical shift of the hole between consecutive pipes (px) — keeps every run solvable |
| `GAME_OVER_DELAY` | `1` | Seconds before restart input is accepted |

## Conventions

- **ES5 only**: `var`, function statements, no arrow functions, no `const`/`let`, no modules, no strict mode, no optional chaining
- Keep the game in a single file; no new dependencies, no build step
- Match existing style: 4-space indent, `function name(){` with space, `//` section comments
- Do not add code comments unless asked
- Run `node --check flappyBird.js` after every change to the game file

## File map

```
index.html       # Page shell: 288×512 canvas, inline CSS, meta/OG tags, favicons
flappyBird.js    # Entire game (single file)
images/          # bg, bird, fg, pipeNorth, pipeSouth sprites
sounds/          # fly.mp3, score.mp3, bgm.wav, gameover.wav
favicon/         # Favicon set + webmanifest
LICENSE          # MIT
CNAME            # Custom domain for GitHub Pages (pyforgedev.web.id)
```

## Gotchas

- The game loop only continues via `requestAnimationFrame` while it keeps requesting frames — the `gameOver` branch must keep the loop alive (it currently always does) or `gameOverT` freezes and restart never unlocks
- `endGame()` resets `gameOverT`; never reset it from the draw loop or the delay never completes
- Canvas is 288×512; spawn/UI math assumes these dimensions
