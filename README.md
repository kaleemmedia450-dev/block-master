# BLOCK MASTER — Premium Block Puzzle

A complete, original 8×8 block puzzle game inspired by classic block-blast mechanics.
All code, art (CSS) and sound (Web Audio synth) are original. No external assets, no
dependencies, no backend, no login — it works fully offline after first load.

## Play it

Open `index.html` in any modern browser (Chrome, Edge, Firefox, Safari, Android, iOS).
That's it — a single self-contained file (~78 KB).

- **Desktop:** drag pieces with the mouse, or click a piece then click the board.
- **Mobile/tablet:** drag with touch, or tap-to-select then tap-to-place.

## What's inside

| Area | Implementation |
|---|---|
| Board | 8×8 grid, 33-shape polyomino library, drag preview (green/red), snap placement |
| Clearing | Full rows + columns, simultaneous multi-line clears, particles, popups, shake |
| Scoring | Central `scoreForLines()` — 1 pt/cell, 10/30/60+ for 1/2/3+ lines, combo multiplier (×1.5 per consecutive clear), +100 full-board clear, golden blocks worth 2× |
| Modes | Classic · Adventure (50 generated levels, targets, move limits, stone obstacles, 3-star ratings, map unlocking) · Time Attack (60/90/120 s, +2 s per line) · Daily Challenge (date-seeded, identical for everyone, streaks, calendar, 🪙50) · Puzzle Challenge (12 hand-built, solver-verified puzzles with fixed pieces) · Zen (soft theme, never game-over) · Endless Pro (harder shapes, 1.5× scoring, own best score) |
| Power-ups | Undo, Hammer, Bomb (3×3), Line Clear, Shuffle, Rotate 90°, Hint (scans every placement, prefers line clears) — inventory-based, bought with earned coins |
| Economy | Coins from clears/levels/dailies/achievements/missions; shop for power-ups + themes. No real money |
| Themes | 11 total (incl. Zen): Classic Blue, Neon, Galaxy, Sunset, Ocean, Forest, Candy, Minimal White, Dark, Gold Luxury — CSS-variable driven, saved |
| Audio | Web Audio API synth: click, pickup, place, invalid, clears, combo, power-up, coin, win/lose + gentle background music loop; separate SFX/music toggles + volume |
| Profile | Local stats (games, blocks, lines, combos, playtime…), 12 achievements with coin rewards, daily missions, local best scores per mode + recent-game history |
| Saving | Versioned `localStorage` save (`blockMasterSaveV1`), auto-saved after every move, Continue button restores unfinished games, safe defaults on corrupted data, full reset in Settings |
| Fairness | Tray generator avoids repeats and guarantees at least one piece fits the current board; game-over checks **every** remaining piece at **every** board position |
| Sharing | Web Share API with clipboard fallback |

## Project structure

The deliverable is a single file for portability (same approach as Brick Blast).
Internally it is organised in clearly commented sections that map 1:1 to the
suggested multi-file layout:

```
index.html
  <style>   → css/style.css + animations.css + responsive.css (theme variables at top)
  <script>  → CONFIG & THEMES            (main.js / config)
              SHAPE_DEFS + geometry      (block-generator.js)
              save/load                  (storage.js)
              Web Audio synth            (audio.js)
              core logic: canPlace,      (board.js / game-engine.js)
                findLines, anyPlacement,
                findHint, scoreForLines  (scoring.js)
              modes & level defs         (game-modes.js / daily-challenge.js)
              rendering, drag & drop     (ui.js)
              power-ups                  (powerups.js)
              achievements & missions    (achievements.js / statistics.js)
```

## Editing

- **Rename the game:** change `CONFIG.gameName` and the `<h1>` logo text in `index.html`.
- **Scoring:** edit `CONFIG.scorePerCell`, `CONFIG.lineBonus`, `CONFIG.clearBoardBonus`.
- **Prices:** `CONFIG.prices`, `CONFIG.themePrices`.
- **Shapes:** add row-string definitions to `SHAPE_DEFS` (e.g. `["XX"," X"]`).
- Core logic is exposed as `window.BM_CORE` for testing.

## QA performed (Nov 2, 2026)

- JavaScript syntax check: pass.
- 26 automated logic tests in a Node harness (board init, all shapes, valid/invalid
  placement, overlap & out-of-bounds rejection, row/column/simultaneous clears,
  scoring table + combo growth, correct game-over semantics incl. the
  one-piece-still-fits case, rotation, hint finding a line-clearing move,
  50 adventure level definitions, daily seed determinism, prefill boards never
  start with a completed line): **26/26 pass**. Two real bugs were found and fixed
  by these tests (hint returned the first placement instead of the best one;
  background music was incorrectly muted by the SFX toggle).
- All 12 Puzzle Challenge boards verified solvable by an exhaustive search solver.
- Every button in the UI is wired to a working function (58 handlers checked).

## QA round 2 (Nov 2, 2026 — deep audit)

- 32-check gameplay flow simulation through the real game functions (fake-DOM
  harness): full Classic game to natural game over, Undo/Shuffle/Hammer/Bomb/
  Line/Hint/Extra, save & restore (board + score identical), Adventure L1,
  Time Attack clock, Daily seed determinism, Puzzle 1 win, exact game-over
  semantics — pass (one threshold in the test itself, "game length > 20 moves",
  was recalibrated after a soak test showed legitimate games end in 17–35 moves).
- Soak test: 12 consecutive Classic games, all ended naturally, scores always
  monotonic, zero errors.
- Bugs found by audit and fixed: (1) lifetime best combo was never written to
  stats, so the Combo Master achievement could never unlock and Statistics
  showed 0; (2) daily-mission progress ignored the day's first game; missions
  now also update live during play; (3) the Daily calendar sheet was
  unreachable — the home banner now opens it (calendar + Play button);
  (4) the brief's "Extra Space" power-up was missing — added as Extra Block
  (bonus 1-cell piece, shop 🪙60).
- An in-browser click-through was attempted twice but the test browser's
  policy blocks both local addresses and the file-host domain; the visual
  pass remains the only unrun check.

## Privacy

Everything is stored only in the player's own browser local storage.
Nothing is collected, tracked or transmitted.
