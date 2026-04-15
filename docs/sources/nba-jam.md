# NBA Jam — Source Citations & Design Reference

## Original Source

| Source | Notes |
|--------|-------|
| [historicalsource/nba-jam](https://github.com/historicalsource/nba-jam) | Original TMS34010 arcade assembly (~30K lines, 125 files). Design reference only — no code is ported. |

## Key Reference Files (from original source)

Understanding the original game's mechanics by studying these files:

| File | Lines | What it documents |
|------|-------|-------------------|
| `PLYR.ASM` | ~12,000 | Player animation, behavior, state machine |
| `DRONE.ASM` + `DRONE2.ASM` | ~4,000 | CPU AI: offensive/defensive decision logic |
| `BB.ASM` + `BB2.ASM` + `BB3.ASM` | ~5,000 | Ball physics, scoring, shot mechanics |
| `DUNK.ASM` | ~2,000 | Dunk detection, animation sequences, special dunks |
| `SELECT.ASM` | ~5,000 | Team/player selection, roster display |
| `ATTRACT.ASM` | ~6,000 | Attract mode, demo gameplay |
| `MENU.ASM` | ~8,000 | Menu system, UI flow |
| `PLYR.EQU` | ~500 | Player data structures, stat fields |
| `GAME.EQU` | ~800 | Game constants, timing, mechanics tuning |
| `HEY.DOC` + `HEY3.DOC` | — | Player stat databases (Speed, Power, Shooting, etc.) |
| `SOUNDS.ASM` + `SPEECH.ASM` | ~5,000 | Sound effect triggers, announcer call table |
| `GSP.EQU` | ~500 | Graphics processor definitions (hardware — reference only) |

## Game Design Extracted from Source

### Player Attributes (9 stats, each 1-10)

From `PLYR.EQU` and `HEY.DOC`:

| Stat | Affects |
|------|---------|
| Speed | Movement velocity, acceleration |
| Power | Push-back on collision, shot contest |
| Shooting | Base accuracy at distance |
| Dunking | Dunk range threshold, special dunk probability |
| Stealing | Steal success radius and probability |
| Blocking | Block timing window, contest range |
| Pass | Pass speed, lead accuracy |
| Clutch | Stat boosts in final 30 seconds |
| Rebounding | Jump timing, rebound contest probability |

### Fire Mechanic

From `GAME.EQU` and gameplay logic in `BB.ASM`:

- 3 consecutive baskets by one player (no opponent score between)
- Activates: unlimited turbo, ~100% shooting, goal tending allowed
- Deactivates: opponent scores any basket
- Visual: player sprite palette swap (flashing), ball trail effect

### Dunk Types

From `DUNK.ASM` — dunk type selected by approach angle, speed, turbo state:

- Standard dunk (straight approach)
- Reverse dunk (angle past basket)
- Tomahawk (turbo + distance)
- Spin dunk (turbo + lateral approach)
- Double-pump (close range + turbo)
- Alley-oop (teammate pass + timing window)

### AI Decision Model

From `DRONE.ASM` — simplified decision tree:

```
OFFENSE:
  if open_for_three AND shooting_stat >= 7 → shoot
  if inside_dunk_range AND dunking_stat >= 6 → dunk
  if teammate_open AND pass_stat >= 5 → pass
  if turbo_available AND lane_open → drive
  else → dribble + create_space

DEFENSE:
  mark_nearest_opponent()
  if ball_handler_in_range → attempt_steal (Stealing stat check)
  if shot_in_progress → contest (Blocking stat + timing)
  else → position_between(opponent, basket)
```

## Platform

| Original | Reimplementation |
|----------|-----------------|
| TMS34010 graphics processor | Software framebuffer (Cyrius) |
| 400x256 resolution | 320x240 or 640x480 |
| Custom DMA sprite engine | Software sprite blitter with scaling |
| DCS audio board | Direct audio output (PCM) |
| Midway arcade cabinet | Keyboard/gamepad input |
