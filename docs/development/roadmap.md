# cyrius-nba-jam Development Roadmap

> **Status**: Scaffolded | **Last Updated**: 2026-04-14

---

## v0.1.0 — Court and Sprites

Get something on screen. Prove the sprite rendering pipeline.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | types.cyr — player stats, game constants | Not started | From HEY.DOC stat model |
| 2 | render.cyr — framebuffer, pixel ops | Not started | Same pattern as cyrius-doom |
| 3 | sprite.cyr — sprite atlas loading, scaled blit | Not started | Key primitive: blit with transparency + scaling |
| 4 | court.cyr — court background with perspective | Not started | Static court, perspective grid |
| 5 | player.cyr — single player entity, basic movement | Not started | Keyboard input, acceleration |
| 6 | main.cyr — game loop, frame timing | Not started | 60fps target |

**Exit criteria**: A player sprite moves around a rendered court.

## v0.2.0 — Ball Physics and Shooting

The ball is the game.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | ball.cyr — parabolic trajectory, gravity | Not started | Fixed-point arc math |
| 2 | shoot.cyr — shot mechanic, accuracy based on stats | Not started | Distance + defender proximity → probability |
| 3 | collision.cyr — ball-rim, ball-backboard bounce | Not started | Restitution coefficient, rim geometry |
| 4 | hud.cyr — score display, shot clock | Not started | |
| 5 | game.cyr — possession, scoring, basic quarter | Not started | |

**Exit criteria**: Player can shoot the ball, it arcs, hits/misses the rim, score updates.

## v0.3.0 — Two Players and Passing

2-on-2 needs teammates.

| # | Item | Status | Notes |
|---|------|--------|-------|
| 1 | Player switching (swap active player) | Not started | |
| 2 | Pass mechanic — lead pass to teammate | Not started | Pass speed based on Pass stat |
| 3 | Steal mechanic — proximity + Stealing stat | Not started | |
| 4 | Basic blocking — contest shots | Not started | Block stat vs shot timing |
| 5 | Turbo meter — boost speed, deplete, recharge | Not started | |

**Exit criteria**: Two human-controlled players pass, steal, block, use turbo.

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
