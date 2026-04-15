# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

## [0.4.0] — 2026-04-14

### Added
- **dunk.cyr** — 351 lines. 6 dunk types with stat/turbo/approach-based selection: Standard, Reverse (approaching from behind), Tomahawk (turbo + far + stat >= 8), Spin (lateral approach + turbo), Double Pump (close + turbo), Alley-Oop (teammate lob). Parabolic leap trajectory interpolating from start position to rim over 45 frames. Ball follows player during ascent, slams through rim at frame 30. Turbo drain on dunk (5 per dunk, fire = free).
- **Alley-oop system** — Automatic detection: ball in flight above hold height + receiver near rim + ball near receiver + receiver has Dunking stat >= 3. Triggers catch-and-dunk with ALLEY_OOP type override.
- **Goal tending** — Ball descending (vz < 0) near rim height in scoring zone = goal tending on block attempt. Automatic 2 points awarded to shooter's team. Checked before block attempt resolves.
- **Dunk-or-shoot auto-detect** — Shoot button near rim with sufficient Dunking stat auto-triggers dunk instead of jump shot. No separate dunk button needed.
- **Dunk state management** — Per-player DunkState struct tracks type, start/target positions, frame counter, scored flag. Cleaned up when dunk animation completes.
- 11 new test assertions (58 total): dunk range/stat/type selection, dunk type names, goal tending (descending/ascending/far), alley-oop conditions

### Project stats
- 13 source modules, 1 test file
- 3906 lines of Cyrius source + 845 lines of tests = 4751 total

## [0.3.0] — 2026-04-14

### Added
- **Player switching** — C key to swap control to teammate on same team
- **Steal mechanic** — V key near ball handler attempts steal. Probability: Stealing stat * 6 out of 100. Success knocks ball loose in random direction. Both success and failure play STEAL animation (committed, 12 frames)
- **Block mechanic** — V key near opponent in SHOOT state attempts block. Probability: Blocking stat * 7 out of 100. Success deflects ball with reversed + randomized velocity. BLOCK animation (committed, 20 frames)
- **Pass interception** — Opposing players within 4.0 radius of a pass in flight automatically intercept (ball given to interceptor). Only triggers on low-altitude balls (z < 8.0)
- **Lead passing** — Pass now aims where teammate will be (current position + velocity * 8 frames ahead). Pass speed scales with Pass stat (stat 10 = 10 frames travel, stat 1 = 19 frames)
- **Turbo refinement** — Scaled drain rates (run=1/frame, shoot=3, dunk=5). Fire mode = infinite turbo. 30-frame cooldown before recharge starts after turbo use
- **Input expansion** — C = switch player, V = steal/block
- 7 new test assertions (47 total): steal success/same-team rejection, block requires SHOOT state, interception close/same-team ignored, turbo drain/fire-infinite

### Project stats
- 12 source modules, 1 test file
- 3479 lines of Cyrius source + 695 lines of tests = 4174 total

## [0.2.0] — 2026-04-14

### Added
- **ball.cyr** — 3D ball entity (x, y, z height) with 5 states (HELD/FLIGHT/BOUNCE/SCORED/DEAD). Semi-implicit Euler physics with gravity. Floor bounce with 0.5 restitution. Air drag. Holder tracking with facing-offset positioning. Shadow rendering at ground level.
- **collision.cyr** — Ball-rim circle collision with 0.7 restitution bounce and push-out. Ball-backboard plane collision with velocity reflection. Ball-player pickup within 6.0 radius. Player-player push-back weighted by Power stat. Scoring detection (ball falling through rim zone). Pre-shifted dist_sq to avoid fixed-point overflow.
- **shoot.cyr** — Shot trajectory computation from player to rim with gravity compensation and arc height. Stat-driven accuracy model: base = Shooting*8, distance penalty, fire bonus (+90). PCG PRNG for make/miss determination. Miss perturbation of velocity. Three-point line detection at 14.0 game units.
- **hud.cyr** — 3x5 pixel bitmap font for digits 0-9 with configurable scale. Score display (home/away), quarter indicator, game clock (MM:SS), shot clock with red warning under 10s. Per-player turbo meter bars with color coding (green/yellow/red). Fire indicator (flashing colored bar).
- **game.cyr** — Full game state machine: TIPOFF(2s) → PLAYING → HALFTIME(5s) → PLAYING → GAMEOVER. 4 quarters at 3:00 each. 24-second shot clock with turnover on violation. Scoring with automatic possession change. Fire mechanic: 3 consecutive baskets activates fire, opponent scoring extinguishes. Overtime on tied score.
- **main.cyr** — Integrated game loop: shoot on space (computes trajectory + accuracy), pass on Z (velocity toward teammate). Ball drawn interleaved with players by depth. Fire indicator. Final score display on game over.
- 16 new test assertions (40 total): ball creation/launch/gravity/bounce/give/follow, dist_sq, ball pickup/no-pickup, PRNG determinism, shot trajectory, three-point detection, game state transitions, clock conversion, turnover, HUD digit rendering, HUD integration

### Project stats
- 12 source modules, 1 test file
- 3255 lines of Cyrius source + 546 lines of tests = 3801 total
- Zero external dependencies

## [0.1.0] — 2026-04-14

### Added
- **render.cyr** — 320x240 framebuffer, 64-color NBA court palette, pixel ops, draw primitives (hline, vline, rect, Bresenham line), PPM screenshot output
- **sprite.cyr** — Sprite struct, blit with transparency (color key 0) and screen clipping, scaled blit with fixed-point stepping for perspective, placeholder player/ball sprite generation, elliptical shadow rendering
- **court.cyr** — Court rendering with perspective projection (far end narrows), wood plank floor with alternating colors, sidelines, baselines, center line/circle, paint areas, three-point arcs, backboard and rim for both baskets, game-to-screen coordinate conversion
- **player.cyr** — 26-field Player struct, 9-stat model from HEY.DOC, state machine with committed states (shoot=30f, dunk=45f, pass=15f, steal=12f, block=20f, fall=30f), acceleration/deceleration movement, turbo meter (drain/recharge), 8-direction facing, court boundary clamping, perspective-scaled drawing
- **input.cyr** — Non-blocking terminal input via termios raw mode, WASD/arrow key movement, space=shoot, z=pass, x=turbo, p=screenshot, q/ESC=quit
- **main.cyr** — Game loop with frame timing (60fps via nanosleep), 4 players at starting positions with per-player stats, depth-sorted rendering (painter's algorithm), active player indicator, `--ppm` flag for headless screenshot mode
- **tests** — 24 assertions: ball physics arc, fire activation/deactivation, stat range validation, framebuffer clear/set/get/bounds, draw primitives, palette values, sprite blit/clipping/placeholder generation, court perspective mapping/scaling, player state machine (transitions, committed rejection, timer expiry), player movement/bounds/facing, court pixel count integration test
- 6 vidya topics created: fixed_point_arithmetic, sprite_rendering, state_machines, collision_detection_2d, game_ai_decisions, projectile_physics (with ASM + Cyrius implementations)

### Project stats
- 7 source modules, 1 test file
- 1749 lines of Cyrius source + 399 lines of tests = 2148 total
- Zero external dependencies
