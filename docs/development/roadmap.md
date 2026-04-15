# cyrius-nba-jam Development Roadmap

> **Status**: v0.5.0 implemented | **Last Updated**: 2026-04-15

---

## v0.1.0 — Court and Sprites

Get something on screen. Prove the sprite rendering pipeline.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | types.cyr — player stats, game constants | Done | 9 enums: Stat, GameState, PlayerState, DunkType, Team, Screen, Court, Fire |
| 2 | render.cyr — framebuffer, pixel ops, PPM output | Done | 292 lines. 64-color palette, draw primitives, Bresenham line, PPM screenshot |
| 3 | sprite.cyr — sprite blit, scaled blit, placeholders | Done | 233 lines. Transparency, clipping, fixed-point scaling, placeholder player/ball generation |
| 4 | court.cyr — court background with perspective | Done | 349 lines. Perspective sidelines, paint, three-point arcs, rims, wood floor planks |
| 5 | player.cyr — player entity, movement, state machine | Done | 365 lines. 26-field Player struct, committed states, acceleration, turbo, facing, perspective draw |
| 6 | input.cyr — keyboard input via termios raw mode | Done | 154 lines. Non-blocking read, WASD/arrows, action buttons, escape sequences |
| 7 | main.cyr — game loop, frame timing, 4 players | Done | 271 lines. Init, update, depth-sorted render, nanosleep timing, --ppm mode |

**Lines**: 1749 source + 399 test = 2148 total across 7 modules + test suite.
**Tests**: 24 assertions across physics, render, sprite, court, player state machine, player movement.

**Exit criteria**: A player sprite moves around a rendered court. ✓ (with --ppm screenshot verification)

## v0.2.0 — Ball Physics and Shooting

The ball is the game.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | ball.cyr — 3D ball entity (x,y,z), semi-implicit Euler, gravity | Done | 303 lines. 5 states (HELD/FLIGHT/BOUNCE/SCORED/DEAD), floor bounce with restitution, air drag, holder tracking |
| 2 | collision.cyr — ball-rim, ball-backboard, ball-player, player-player | Done | 343 lines. Circle collision with pre-shifted dist_sq, rim bounce, backboard reflection, pickup radius, power-weighted push-back |
| 3 | shoot.cyr — shot trajectory, accuracy model, make/miss | Done | 227 lines. PCG PRNG, trajectory from physics (gravity compensation + arc height), stat-driven accuracy with distance penalty, miss perturbation, three-point detection |
| 4 | hud.cyr — scoreboard, shot clock, turbo meters, fire indicator | Done | 197 lines. 3x5 bitmap font, digit rendering with scale, score/quarter/clock display, per-player turbo bars, fire flash |
| 5 | game.cyr — game state machine, possession, scoring, quarters, fire | Done | 319 lines. TIPOFF→PLAYING→HALFTIME→GAMEOVER flow, shot clock (24s), quarter clock (3:00), scoring with possession change, fire activation/extinguishment, turnovers |
| 6 | main.cyr — integrated game loop with all systems | Done | 388 lines. Shoot/pass input handling, ball-player interleaved depth sort, fire indicator, final score display |

**Lines**: 3255 source + 546 test = 3801 total across 12 modules + test suite.
**Tests**: 40 assertions across all systems (ball, collision, shooting, game state, HUD, + all v0.1.0 tests).

**Exit criteria**: Player can shoot the ball, it arcs, hits/misses the rim, score updates. ✓

## v0.3.0 — Two Players and Passing

2-on-2 needs teammates.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | Player switching (C key, same team) | Done | Cycles to teammate, auto-switch on turnover planned |
| 2 | Lead passing — aim where teammate will be | Done | Position + velocity * 8 frames, speed scales with Pass stat (10→10f, 1→19f) |
| 3 | Steal mechanic — V key, proximity + Stealing stat | Done | Probability: stat*6/100, knocks ball loose, committed STEAL animation (12f) |
| 4 | Block mechanic — V key near shooter, Blocking stat | Done | Probability: stat*7/100, deflects with reversed+random velocity, committed BLOCK animation (20f) |
| 5 | Pass interception — auto-intercept in flight path | Done | Opposing player within 4.0 radius of low-altitude pass catches it |
| 6 | Turbo refinement — scaled drain, fire infinite, cooldown | Done | Run=1/f, shoot=3, dunk=5. 30f cooldown before recharge. Fire = no drain |

**Lines**: 3479 source + 695 test = 4174 total.
**Tests**: 47 assertions (7 new: steal, block, intercept, turbo).

**Exit criteria**: Two human-controlled players pass, steal, block, use turbo. ✓

## v0.4.0 — Dunks

The spectacle. BOOMSHAKALAKA!

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | dunk.cyr — dunk detection + auto-shoot-or-dunk | Done | 351 lines. Proximity + Dunking stat >= 3, auto-detect near rim |
| 2 | 6 dunk types with selection logic | Done | Standard, Reverse, Tomahawk, Spin, Double Pump, Alley-Oop. Selected by approach angle, distance, turbo, stat |
| 3 | Dunk trajectory — parabolic leap to rim | Done | 45-frame committed animation. Interpolate position start→rim, parabolic height arc. Ball follows, slams at frame 30 |
| 4 | Alley-oop — teammate lob + receiver near rim | Done | Auto-detect: ball in flight + high + receiver near rim + ball near receiver. Catches and immediate dunk |
| 5 | Goal tending detection | Done | Ball descending near rim = goal tending on block. Auto-scores 2 points |
| 6 | Per-player DunkState management | Done | Tracks type, positions, frame counter, scored flag. Cleaned up on completion |

**Lines**: 3906 source + 845 test = 4751 total across 13 modules.
**Tests**: 58 assertions (11 new: dunk range/stat/type, goal tending, alley-oop).

**Exit criteria**: Players can dunk, with multiple animation types and alley-oops. ✓

## v0.5.0 — Drone AI

The game plays back.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | ai.cyr — offensive decisions (shoot/pass/drive/dunk) | Done | 723 lines. Ball handler AI: dunk priority near rim, stat-driven shot selection, lead pass to open teammates, drive toward basket. Off-ball: positional movement for pass reception |
| 2 | Defensive AI — mark player, contest shots, steal | Done | Chase ball handler, position between handler and basket, steal/block attempts with cooldowns, goal tending check before blocks, loose ball pursuit, mark nearest opponent |
| 3 | AI difficulty scaling — attribute modifiers per difficulty | Done | 3 levels: Easy (halved probs, 20f delay), Medium (baseline, 10f delay), Hard (1.5x probs, 3f delay, turbo chase) |
| 4 | Teammate AI when not player-controlled | Done | Off-ball offense positioning, defensive marking. C key switches human control + toggles AI active state |

**Lines**: 4654 source + 1120 test = 5774 total across 14 modules.
**Tests**: 76 assertions (18 new: AI init, decisions, movement, difficulty, openness).

**Exit criteria**: Full 2-on-2 game against AI opponents. Competitive, not trivial. ✓

## v0.6.0 — Game Flow Polish

Close the gaps in the gameplay loop. Fire, game flow, and the state machine already work (v0.2.0/v0.4.0). This version fills in the missing mechanics and dead-ball handling.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | Inbound after scoring — reset ball to scoring team's baseline, give to player | Not started | Currently ball goes DEAD with no recovery |
| 2 | Rebounding — PS_REBOUND state, Rebounding stat contest, jump timing near rim | Not started | PS_REBOUND enum exists but unused |
| 3 | Clutch stat activation — boost stats in final 30 seconds of quarter | Not started | Stat defined in types.cyr but never checked |
| 4 | Pushing fouls — excessive contact triggers foul, free inbound | Not started | Player-player collision exists but no foul detection |
| 5 | Auto-switch on turnover — human player auto-switches to nearest teammate with ball | Not started | Currently must manually press C |
| 6 | Dead ball recovery — handle BALL_DEAD state, inbound timer, possession reset | Not started | |

**Exit criteria**: Uninterrupted game from tip-off to final buzzer. No dead states. All 9 stats actively affect gameplay.

## v0.7.0 — Menus, Roster, and Audio

Team select, attract mode, announcer.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | roster.cyr — team/player database with stats from HEY.DOC | Not started | Stat tables for all teams |
| 2 | menu.cyr — team select screen with cursor navigation | Not started | |
| 3 | Attract mode — AI vs AI demo gameplay on title screen | Not started | AI system (v0.5.0) enables this |
| 4 | audio.cyr — sound effects, announcer text calls (BOOMSHAKALAKA) | Not started | Text-based first, PCM later |
| 5 | Visual fire effects — sprite palette flash, ball trail during fire mode | Not started | Currently only jersey color swap |
| 6 | Animation polish — smooth sprite transitions between states | Not started | |

**Exit criteria**: Player can select a team, play a full game with announcer calls, and return to menu.

## v1.0.0 — Complete Game

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | All game systems complete and hardened | Not started | |
| 2 | Security audit (input handling, asset loading) | Not started | |
| 3 | Performance: stable 60fps with all effects | Not started | |
| 4 | Fuzz: asset loading, save data | Not started | |
| 5 | Difficulty selection (Easy/Medium/Hard) at menu | Not started | AI difficulty system exists (v0.5.0), needs UI |

## Future

- **Multiplayer** — local split-screen or network play
- **kiran integration** — extract sprite/animation/AI systems into kiran APIs
- **impetus integration** — extract physics into impetus collision/trajectory APIs
- **Custom rosters** — user-defined teams and players
- **Tournament mode** — bracket progression

## What This Exercises for kiran/impetus

| Game System | kiran/impetus API it validates |
|-------------|-------------------------------|
| Sprite atlas + scaling | kiran 2D sprite renderer |
| Entity management (players, ball, effects) | kiran ECS architecture |
| Game state machine | impetus simulation stepping |
| AI decision trees | kiran/joshua behavior system |
| 2D collision (player, ball, rim) | impetus collision interface |
| Animation sequencing | kiran sprite animation |
| Input mapping | kiran input abstraction |
| Frame timing | kiran game loop |

---

*Last Updated: 2026-04-15*
