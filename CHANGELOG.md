# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

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
