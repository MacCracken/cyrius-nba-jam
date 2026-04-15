# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/).

## [Unreleased]

## [0.7.0] — 2026-04-15

### Added
- **roster.cyr** — 337 lines. 8 teams with 2 players each (16 total). Full 9-stat profiles per player inspired by original HEY.DOC archetypes. Team names, player names, jersey color pairs. Stat lookup by slot, roster_apply_stats to load stats onto Player struct, team stat totals for display. Teams: Chicago, New York, Detroit, Phoenix, Orlando, Seattle, Houston, San Antonio.
- **menu.cyr** — 337 lines. Title screen with block-letter "NBA JAM" rendering (draw_rect composition). Team select screen with cursor navigation, team list with jersey color swatches, stat bar display panel showing Speed/Power/Shooting/Dunking/Stealing/Blocking for both players. Two-phase selection (home then away), back button support, idle timer for attract mode trigger.
- **audio.cyr** — 229 lines. Text-based announcer call system with priority levels (Low/Medium/High/Fire). Score calls: "BOOMSHAKALAKA!", "FROM DOWNTOWN!", "NOTHING BUT NET!", "HE'S HEATING UP!", "HE'S ON FIRE!" with consecutive basket tracking. Dunk calls: "MONSTER JAM!", "IS IT THE SHOES?!". Defense calls: "REJECTED!", "STOLEN!". Game calls: "OVERTIME!", "AT THE BUZZER!", "PUSHING FOUL!". Announcer text rendered as centered bar with flash effect for high-priority calls.
- **Attract mode** — All 4 players AI-controlled with random teams. Triggered after 10 seconds idle on title screen. "PRESS START" overlay flashes. Any key returns to menu. Game over returns to menu.
- **Visual fire effects** — On-fire player jersey flashes through yellow/orange/red on 12-frame cycle. Ball trail effect: 3 trailing segments (yellow→orange→red) drawn behind ball in flight when last shooter is on fire. State-based player color: white flash during SHOOT, red flash during BLOCK, yellow flash during STEAL.
- **Game flow restructure** — main.cyr refactored to mode-based dispatch: menu (0), team select (1), playing (2), attract (3). game_start_match creates players from roster with team-colored sprites. Score change detection triggers announcer calls. Foul state triggers announcer. Game over returns to menu.
- 24 new test assertions (121 total): roster team count/stat range/apply/name/total, menu init/select-home/select-both, announcer trigger/priority/expiry/fire-call, fire visual effect

### Project stats
- 17 source modules, 1 test file
- 6183 lines of Cyrius source + 1536 lines of tests = 7719 total

## [0.6.0] — 2026-04-15

### Added
- **Inbound system** — STATE_INBOUND game state. After scoring, ball enters SCORED (20f) then DEAD. Dead ball now triggers inbound: ball reset to receiving team's baseline, given to nearest team player, 1.5-second pause before play resumes with fresh shot clock. No more stuck dead balls.
- **Dead ball recovery** — BALL_DEAD state now properly detected in game_update_playing and transitions to inbound flow instead of hanging forever.
- **Rebounding** — collision_check_rebound contests loose balls near the rim. Players within 8.0 of rim compete based on Rebounding stat (stat * 10 + random 0-50). Winner receives ball and enters committed PS_REBOUND state (20 frames). Rebounding stat (previously defined but unused) now actively affects gameplay.
- **Clutch stat activation** — Final 30 seconds of each quarter triggers clutch time. All players receive stat boosts: Shooting, Stealing, Blocking, and Speed each gain Clutch/2 points (capped at 10). Applied once per quarter, reset on quarter change. Clutch stat (previously defined but unused) now actively affects gameplay.
- **Pushing fouls** — Per-pair push counter tracks player-player collisions in a 1.5-second window. 4+ collisions between opposing players triggers a pushing foul. STATE_FOUL game state with 2-second display. Possession awarded to fouled team with inbound. Foul counts tracked per team per quarter, reset on quarter change. Pusher determined by momentum direction toward the other player.
- **Auto-switch on turnover** — When possession changes (score, turnover, steal), human player automatically switches to the nearest teammate on their team relative to the ball. AI active flags updated accordingly. No more manual C-key switching after every change of possession.
- **PS_REBOUND committed state** — 20-frame committed animation for rebound grab. Added to player_is_committed and timer assignment.
- **Game struct expansion** — 4 new fields: foul_team, home_fouls, away_fouls, clutch_active. Quarter state (fouls, clutch) reset via game_reset_quarter_state on quarter transitions.
- 21 new test assertions (97 total): inbound state/ball/timer, rebound near-rim/far, clutch boost/cap, foul state/possession/count, push foul detection, auto-switch nearest, PS_REBOUND committed/timer

### Project stats
- 14 source modules, 1 test file
- 5109 lines of Cyrius source + 1362 lines of tests = 6471 total

## [0.5.0] — 2026-04-15

### Added
- **ai.cyr** — 723 lines. Full drone AI for CPU-controlled players. Offensive AI: ball handler evaluates dunk (priority when in range), shoot (stat-driven accuracy + distance + fire awareness), pass (teammate openness + positional advantage + pressure detection), drive (direction toward basket with turbo). Off-ball offense: positional movement for pass reception, alley-oop zone seeking. Defensive AI: chase ball handler with positioning between handler and basket, steal attempts (proximity + cooldown + difficulty-adjusted probability), block attempts (detects shooter state, checks goal tending first), loose ball pursuit. Mark nearest opponent when off-ball defense.
- **AI difficulty system** — 3 levels (Easy/Medium/Hard). Easy: halved action probabilities, 20-frame reaction delay. Medium: baseline probabilities, 10-frame reaction delay. Hard: 1.5x probabilities, 3-frame near-instant reactions, turbo usage when chasing.
- **AI state management** — Per-player AIState struct with action cooldowns (shoot=30f, pass=20f, steal=40f, block=30f), drive reassessment timer (15f), reaction delay, turbo intent. Prevents jittery decision-making.
- **Teammate AI** — Human's teammate runs off-ball offense (positioning for passes) or defensive marking when opponent has possession. Player switching (C key) toggles AI active/inactive on swap.
- **AI integration** — ai_update_all called each frame for all non-human players. AI uses same game functions as human input (player_apply_input, shoot_ball, dunk_start, collision_try_steal/block, ball_launch for passes). Rim targeting accounts for team direction (home shoots right, away shoots left).
- 18 new test assertions (76 total): AI init/active/difficulty, nearest opponent/teammate finding, offensive shoot-or-dunk near rim, defensive chasing, off-ball movement, inactive AI no-op, difficulty probability scaling, direction computation, player openness detection

### Project stats
- 14 source modules, 1 test file
- 4654 lines of Cyrius source + 1120 lines of tests = 5774 total

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
