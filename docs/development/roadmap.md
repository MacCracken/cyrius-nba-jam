# cyrius-nba-jam Development Roadmap

> **Status**: v0.5.0 — game systems complete, placeholder art | **Last Updated**: 2026-04-15

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

## v0.5.0 — All Game Systems (Placeholder Art)

Everything from AI through menus, hardening, and visual polish. All game mechanics work. All placeholder art — no real sprites loaded.

| # | System | Status | Notes |
|---|--------|--------|-------|
| 1 | Drone AI (ai.cyr, 723 lines) | Done | Offensive/defensive AI, 3 difficulty levels, teammate AI, off-ball positioning |
| 2 | Game flow polish | Done | Inbound, rebounding, clutch stat, pushing fouls, auto-switch on turnover, dead ball recovery |
| 3 | Menus + roster (menu.cyr, roster.cyr) | Done | Title screen, team select with stat bars, difficulty select. 8 teams, 16 players |
| 4 | Announcer (audio.cyr) | Done | Text-based bitmap font announcer. BOOMSHAKALAKA, HE'S ON FIRE, REJECTED, etc. |
| 5 | Visual effects | Done | Fire jersey flash, ball trail, state-based player color, 2px court lines, circle ball |
| 6 | Hardening + security audit | Done | Bounds clamping, div-by-zero guards, null checks, frame budget tracking |
| 7 | PPM screenshot mode | Done | 7 screenshots covering title/select/difficulty/game/action/fire/dunk |

**Lines**: ~6400 source + ~1660 test = ~8060 total across 17 modules.
**Tests**: 132 assertions.

**Exit criteria**: Complete game loop from title screen through gameplay to game over, with AI opponents, all 9 stats active, announcer calls, fire mechanic, difficulty selection. All placeholder art. ✓

## v0.6.0 — T-Unit Asset Extractor

Build tooling to decode Williams arcade hardware sprite format.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | T-Unit IMG format reverse-engineering | Not started | Study DMA transfer format from historicalsource/nba-jam-tournament-edition ASM. Map headers, pixel data layout, palette references |
| 2 | img-extract tool — decode .IMG files to PNG sprite sheets | Not started | Read IMG/ directory files (COURT.IMG, BALL.IMG, DUNKS.IMG, etc.), output indexed-color PNGs |
| 3 | Palette extraction from ASM color tables | Not started | Extract flesh tones, uniform colors, trim colors from PAT tables in assembly |
| 4 | Cross-reference with MAME ROM dumps | Not started | Use MAME debug tools on Chewlix cabinet to dump sprites/palettes as validation |
| 5 | Sprite sheet catalog — document all extracted assets | Not started | Map every sprite to its game function (player idle, run, shoot, dunk frames, etc.) |

**Exit criteria**: All game sprites extracted as indexed-color PNGs with correct palettes.

## v0.7.0 — Real Asset Loading

Replace placeholder geometry with extracted arcade sprites.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | Sprite atlas loader — load PNG sprite sheets at startup | Not started | Parse indexed-color images, load into framebuffer-compatible format |
| 2 | Player sprite animation — frame sequences for all states | Not started | Idle, run, shoot, dunk (6 types), steal, block, fall, rebound. Per-direction frames |
| 3 | Court background — load COURT.IMG extracted background | Not started | Replace procedural court_draw with sprite blit of actual court |
| 4 | Ball sprite — replace circle with real ball frames | Not started | BALL.IMG, rotation frames, fire ball variant |
| 5 | HUD sprites — scoreboard, shot clock, turbo meters | Not started | Replace bitmap font with extracted HUD graphics |
| 6 | Palette system — team-specific palette swaps at runtime | Not started | Same base sprite, different palette per team (matching original PAT system) |

**Exit criteria**: Game looks like NBA Jam. Same sprites, same court, same feel as the arcade unit.

## v0.8.0 — Audio

Sound effects and digitized speech.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | PCM audio output via ALSA | Not started | Same approach as cyrius-doom audio system |
| 2 | Sound effect triggers — swish, bounce, buzzer, whistle | Not started | Map game events to PCM samples |
| 3 | Digitized announcer speech — extract from DCS audio data | Not started | "BOOMSHAKALAKA!", "HE'S ON FIRE!", "FROM DOWNTOWN!" |
| 4 | Crowd noise — ambient background audio | Not started | |

**Exit criteria**: Game sounds like NBA Jam.

## v1.0.0 — Arcade-Authentic Complete Game

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | All real assets loaded and rendering correctly | Not started | Sprites, court, ball, HUD, palettes |
| 2 | Audio complete — effects + announcer + crowd | Not started | |
| 3 | Performance: stable 60fps with all real sprites | Not started | Benchmark with full sprite loads |
| 4 | Play-test on Chewlix cabinet | Not started | Arcade-authentic feel verification on real hardware |
| 5 | Security audit — asset loading paths, file parsing | Not started | Fuzz .IMG loader, validate all external input |

**Exit criteria**: Indistinguishable from the arcade. Runs on the Chewlix cabinet.

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
