# Neon Alley Arcade

A six-game arcade cabinet, built entirely as static HTML/CSS/JS — no build step, no dependencies, no backend. Open a file in a browser and play.

Every game is an original implementation inspired by a classic arcade genre, with its own title and characters rather than reusing trademarked names or assets:

| Game | Genre | Notes |
|---|---|---|
| **Brick Blitz** | Brick breaker | 10 hand-built levels, power-ups, combo scoring |
| **Pellet Panic** | Maze chase | 4 ghosts, each with a genuinely distinct AI targeting strategy |
| **Starfront Siege** | Space shooter | Endless escalating waves, destructible bunkers, UFO bonus |
| **Vector Drift** | Asteroid field | True frictionless drift physics, splitting asteroids, hyperspace |
| **Neon Rally** | Pong-style volley | 1-player vs CPU, or 2-player local |
| **Neon Coil** | Snake-style grid crawler | Speed ramps up as you grow, bonus food spawns |

## Play it

**Easiest:** open [`neon_alley_arcade.html`](./neon_alley_arcade.html) directly in any modern browser. This is the full cabinet — a single self-contained file with all six games base64-embedded inside it. No other files are required; you can move just this one file anywhere and it still works.

**Individually:** each game also exists as its own standalone file under [`games/`](./games), in case you want to open, edit, or embed just one of them.

## How it's built

- Pure HTML5 Canvas + vanilla JavaScript — no frameworks, no npm install, no build tooling.
- Every sprite, ship, ghost, and alien is drawn live with code (arcs, paths, small pixel-grids) — there are no image assets anywhere in this project.
- Sound effects are synthesized at runtime via the Web Audio API — no audio files either.
- The cabinet (`neon_alley_arcade.html`) wraps each game in an `<iframe srcdoc>` and scales it to fit the available screen space using a measured-natural-size + CSS `transform: scale()` approach, so every game fits its allotted space exactly regardless of its native aspect ratio or window size — no internal scrollbars, ever.
- All games use only keyboard input (arrows/WASD, Space, Shift, P, M), which is what makes a real physical cabinet build straightforward: a USB arcade encoder board makes a joystick + buttons act like a keyboard, so no code changes are needed to run this on real hardware.

See [`docs/arcade_build_guide.md`](./docs/arcade_build_guide.md) for a full parts list and step-by-step guide to building a real cabinet around this someday.

## Repo structure

```
neon-alley-arcade/
├── neon_alley_arcade.html   ← the full cabinet (all 6 games embedded, open this one)
├── games/                   ← each game as its own standalone file
│   ├── brick_blitz.html
│   ├── pellet_panic.html
│   ├── starfront_siege.html
│   ├── vector_drift.html
│   ├── neon_rally.html
│   └── neon_coil.html
└── docs/
    └── arcade_build_guide.md
```

## Notes

- No persistence: scores reset on refresh. There's no backend and no `localStorage` use by design (it's unreliable inside the cabinet's embedded `iframe`s).
- The cabinet pulls two Google Fonts (`Press Start 2P`, `Space Mono`) over the network for the retro-arcade look; it still works fully offline, just with a fallback monospace font.
- Built collaboratively with Claude (Anthropic) across an extended conversation — game logic, AI behavior, and physics were each verified with headless automated tests during development, not just eyeballed.
