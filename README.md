# cyrius-nba-jam — NBA Jam in Cyrius

2-on-2 arcade basketball reimplemented in Cyrius. Software-rendered, zero dependencies.

Inspired by Midway's NBA Jam (1993). Original arcade source ([historicalsource/nba-jam](https://github.com/historicalsource/nba-jam)) used as design reference — all code is new Cyrius.

## Features (planned)

- 2-on-2 basketball with turbo, dunks, and steals
- "HE'S ON FIRE!" mechanic (3 consecutive baskets)
- Drone AI with offensive/defensive decision trees
- 2D sprite rendering with perspective scaling
- 60fps software renderer

## Build

```sh
cyrius build src/main.cyr build/nba-jam
./build/nba-jam
```

## Why

This project validates the 2D game engine stack for AGNOS:
- **Sprite rendering** — what kiran needs for 2D games
- **Game state machines** — what impetus needs for simulation
- **AI decision trees** — what joshua needs for NPCs
- **2D collision** — what impetus needs for physics

cyrius-doom proved 3D. This proves 2D. Both feed the engine.

## License

GPL-3.0-only. Game assets are user-provided (not distributed).
