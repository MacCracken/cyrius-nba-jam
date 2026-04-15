# cyrius-nba-jam Development Roadmap

> **Status**: v0.1.0 implemented | **Last Updated**: 2026-04-14

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

The spectacle.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | dunk.cyr — dunk detection (proximity + angle + Dunking stat) | Not started | |
| 2 | Dunk animation sequences — committed (can't cancel) | Not started | From DUNK.ASM reference |
| 3 | Special dunks — flip, spin, tomahawk | Not started | Triggered by turbo + angle |
| 4 | Alley-oop — teammate position + timing window | Not started | |
| 5 | Goal tending detection | Not started | |

**Exit criteria**: Players can dunk, with multiple animation types and alley-oops.

## v0.5.0 — Drone AI

The game plays back.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | ai.cyr — offensive decisions (shoot/pass/drive/dunk) | Not started | From DRONE.ASM logic |
| 2 | Defensive AI — mark player, contest shots, steal | Not started | From DRONE2.ASM logic |
| 3 | AI difficulty scaling — attribute modifiers per difficulty | Not started | |
| 4 | Teammate AI when not player-controlled | Not started | |

**Exit criteria**: Full 2-on-2 game against AI opponents. Competitive, not trivial.

## v0.6.0 — Fire and Game Flow

HE'S ON FIRE!

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | Fire mechanic — 3 consecutive baskets, unlimited turbo, 100% shooting | Not started | |
| 2 | Full game flow — 4 quarters, halftime, overtime | Not started | |
| 3 | Fouls — pushing, goal tending | Not started | |
| 4 | game.cyr — complete state machine | Not started | |

**Exit criteria**: Complete game from tip-off to final buzzer with fire mechanic.

## v0.7.0 — Menus and Polish

Team select, attract mode, audio.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | menu.cyr — team select screen | Not started | |
| 2 | roster.cyr — team/player database with stats | Not started | |
| 3 | Attract mode — demo gameplay | Not started | |
| 4 | audio.cyr — sound effects, announcer calls | Not started | |
| 5 | Visual polish — shadows, smooth animations | Not started | |

## v1.0.0 — Complete Game

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | All game systems complete and hardened | Not started | |
| 2 | Security audit (input handling, asset loading) | Not started | |
| 3 | Performance: stable 60fps with all effects | Not started | |
| 4 | Fuzz: asset loading, save data | Not started | |

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

*Last Updated: 2026-04-14*
