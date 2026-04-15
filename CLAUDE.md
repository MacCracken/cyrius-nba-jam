# cyrius-nba-jam — Claude Code Instructions

## Project Identity

**cyrius-nba-jam** — NBA Jam reimplementation in Cyrius. 2-on-2 arcade basketball with digitized sprites, turbo meter, special dunks, and "HE'S ON FIRE!" mechanic.

- **Type**: Standalone game binary (Cyrius-native, no port)
- **License**: GPL-3.0-only
- **Version**: SemVer `0.D.M` pre-1.0
- **Version file**: `VERSION` at repo root
- **Genesis repo**: [agnosticos](https://github.com/MacCracken/agnosticos)
- **Reference source**: [historicalsource/nba-jam](https://github.com/historicalsource/nba-jam) (TMS34010 assembly — design reference, not ported code)
- **Sister project**: [cyrius-doom](https://github.com/MacCracken/cyrius-doom) — same sprint model, different rendering path

## What This Is

A clean-room reimplementation of NBA Jam's game design in Cyrius. The original is ~30K lines of TMS34010 assembly welded to Williams/Midway arcade hardware. We reimplement the game mechanics, physics, AI, and rendering using the original source as a design reference — studying how the game works, not translating its assembly.

**This is NOT a port.** The original assembly is a reference for game design (stat models, AI decisions, dunk tables, physics tuning). All code is new Cyrius.

## Why This Project Exists

1. **Proves Cyrius for 2D games.** cyrius-doom proved 3D BSP rendering. This proves sprite-atlas rendering, scaling, animation sequencing — a different rendering path.

2. **Exercises the game engine stack.** Every system this game needs is a system kiran (game engine) and impetus (physics) need to provide:
   - Sprite atlas + scaling → kiran's 2D rendering
   - ECS-style entity management → kiran's architecture
   - Game state machine → impetus simulation stepping
   - AI decision trees → kiran/joshua behavior system
   - 2D collision → impetus collision interface
   - Animation sequencing → kiran sprite animation

3. **Port-feedback loop.** The same pattern as cyrius-doom → Cyrius compiler improvements. This project surfaces what kiran and impetus need as APIs, driving their roadmaps with real consumer data.

## Architecture

```
src/
  main.cyr          — Entry point, game loop, frame timing
  types.cyr         — Shared types, constants, player stats
  court.cyr         — Court rendering, background, perspective
  sprite.cyr        — Sprite atlas loader, scaling, animation frames
  player.cyr        — Player entity, movement, state machine
  ball.cyr          — Ball physics, trajectory arcs, bounce
  dunk.cyr          — Dunk detection, animation sequences, special dunks
  shoot.cyr         — Shot mechanics, accuracy model, shot clock
  ai.cyr            — Drone AI: pass/shoot/dunk/defend decisions
  collision.cyr     — Player-player, ball-rim, ball-backboard, ball-player
  game.cyr          — Game state: quarters, score, possession, fouls, fire
  hud.cyr           — Scoreboard, shot clock, turbo meter, player names
  input.cyr         — Keyboard/gamepad input mapping
  menu.cyr          — Team select, player select, attract mode
  render.cyr        — Framebuffer, sprite compositing, draw order
  audio.cyr         — Sound effects, announcer calls (BOOMSHAKALAKA)
  roster.cyr        — Team/player database, stat attributes
```

### Key Game Systems

**Player Stats** (from original source `HEY.DOC`):
- Speed, Power, Shooting, Dunking, Stealing, Blocking, Pass, Clutch, Rebounding
- Each 1-10 scale, affects probability curves for actions

**Fire Mechanic:**
- 3 consecutive baskets without opponent scoring → "HE'S ON FIRE!"
- Fire mode: unlimited turbo, 100% shooting, goal tending allowed
- Opponent scores → fire extinguished

**Drone AI** (from original `DRONE.ASM`, `DRONE2.ASM`):
- Offensive: evaluate shoot vs pass vs drive vs dunk based on distance, defender position, shot clock
- Defensive: mark nearest opponent, contest shots, steal attempts
- Alley-oop detection: teammate position + trajectory + timing window

**Physics:**
- Ball: parabolic arc with configurable gravity, bounce off rim/backboard with restitution
- Players: acceleration/deceleration with turbo modifier, collision push-back
- Court: 2D with perspective scaling (players smaller at far end)

## Development Process

### Sprint Model (from cyrius-doom)

This follows the same pattern as cyrius-doom: intensive sprints with field notes.

### Work Loop (continuous)

1. Work phase — implement game system, test visually
2. Build: `cyrius build src/main.cyr build/nba-jam`
3. Test: `cyrius test`
4. Play test — run the game, verify feel and timing
5. Benchmark: frame time (target: <16ms for 60fps)
6. Documentation — CHANGELOG, field notes
7. Return to step 1

### Task Sizing

- **Low/Medium effort**: Batch freely
- **Large effort**: Small bites — one game system per sprint
- **If unsure**: Treat as large

## DO NOT

- **Do not commit or push** — the user handles all git operations
- **NEVER use `gh` CLI** — use `curl` to GitHub API only
- Do not add unnecessary dependencies
- Do not use the original TMS34010 assembly as code — it is a DESIGN REFERENCE only
- Do not distribute NBA-licensed content (team names, player likenesses are user-provided assets)
- **Study the original source** before implementing any system — understand the design, then write fresh Cyrius

## Asset Strategy

The game engine is GPL-3.0. Game assets (sprites, sounds, team data) are **not distributed** — they're either:
- User-provided from their own legally obtained copy
- Original placeholder art (geometric shapes, generic players)
- The `assets/` directory is gitignored for binary content

The engine must work with placeholder assets. NBA-specific content is an overlay, not a dependency.

## Rendering Approach

**Framebuffer-based** (same as cyrius-doom):
- 320x240 or 640x480 framebuffer
- Sprite compositing with z-order (court → shadows → players → ball → HUD)
- Perspective scaling: sprites shrink toward the far baseline
- No GPU dependency — pure software rendering in the framebuffer

**Key difference from DOOM:** DOOM renders columns (vertical slices). NBA Jam renders sprites (rectangular blits with transparency). The sprite renderer is the core primitive — scaled blit with color key.

## Testing Strategy

- **Unit tests**: Physics (ball arc, bounce angles), collision detection, stat calculations, AI decisions
- **Visual tests**: Render individual sprites, court perspective, HUD layout
- **Integration**: Full game loop at 60fps with placeholder art
- **Benchmarks**: Frame time, sprite count vs frame time, AI decision time
- **Play testing**: Feel — does turbo feel right? Do dunks have weight? Is the AI competitive?

## Documentation Structure

```
Root files (required):
  README.md, CHANGELOG.md, CLAUDE.md, CONTRIBUTING.md, SECURITY.md, CODE_OF_CONDUCT.md, LICENSE

docs/ (required):
  development/roadmap.md — phased milestones
  sources/nba-jam.md — reference source citations, original game mechanics documentation
```

## CHANGELOG Format

Follow [Keep a Changelog](https://keepachangelog.com/). Frame time benchmarks for rendering claims.
