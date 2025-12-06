# Fantasy Forge Engine

![Fantasy Forge Engine Cover](https://github.com/AiLang-Author/Fantasy-Forge-Engine-/raw/main/dnd/ailang/assets/FFE%20Cover%20Pic.jpg)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub issues](https://img.shields.io/github/issues/AiLang-Author/Fantasy-Forge-Engine-)](https://github.com/AiLang-Author/Fantasy-Forge-Engine-/issues)
[![GitHub stars](https://img.shields.io/github/stars/AiLang-Author/Fantasy-Forge-Engine-)](https://github.com/AiLang-Author/Fantasy-Forge-Engine-/stargazers)

A fully data-driven, open-source RPG engine inspired by Dragon Quest and tabletop D&D. Built in AILang, it compiles to a compact native binary (~250KB) and renders to terminal or browser. Create entire campaigns without touching source code.

```
    ╔════════════════════════════════════╗
    ║  THE GOBLIN CAVES                  ║
    ╠════════════════════════════════════╣
    ║  ##########   HP: 45/52           ║
    ║  #........#   MP: 12/20           ║
    ║  #..@.M...#   LV: 7               ║
    ║  #........#   Gold: 1,250         ║
    ║  ####+#####                        ║
    ║                                    ║
    ║  A Goblin appears!                 ║
    ╚════════════════════════════════════╝
```

## Why Fantasy Forge?

- **Zero recompilation modding** — Maps, monsters, items, shops, and quests are all text files
- **Tiny footprint** — Native binary under 250KB, runs anywhere Linux runs
- **Retro feel, modern extensibility** — Dragon Quest mechanics with multiplayer on the roadmap
- **True D&D platform** — Built for community campaigns and shared worlds

## Features

| Category | What's Included |
|----------|-----------------|
| **Combat** | Turn-based battles, d20-style rolls, skills, advantage/disadvantage |
| **Progression** | Classes, leveling, stat growth, learnable skills |
| **World** | Multi-map worlds with portals, towns, dungeons, overworlds |
| **Economy** | Shops (buy/sell rates, restocking), inns (rest/save checkpoints) |
| **Persistence** | 3 save slots, party support (up to 4), quest flags, checksum validation |
| **Encounters** | Zone-based random battles with configurable rates and monster groups |
| **Rendering** | Terminal TUI with ANSI colors, JSON output for browser integration "in development" |

## Quick Start

## Prerequisites

- [AILang compiler](https://github.com/AiLang-Author/AiLang/tree/master/ailang)
- Python 3.x (to run compiler)
- Linux (x86-64) >>>> Other operating system support is coming soon with compiler support. Haiku OS, BSD and more.

## Build & Run

```bash
# Clone the engine
git clone https://github.com/AiLang-Author/Fantasy-Forge-Engine-.git
cd Fantasy-Forge-Engine-/dnd/ailang

# Clone the AILang compiler (if you don't have it)
git clone https://github.com/AiLang-Author/AiLang.git

# Compile (outputs dnd_exec by default, or use -o to specify)
python3 AiLang/ailang/main.py dnd.ailang
# Or with custom output name:
# python3 AiLang/ailang/main.py dnd.ailang -o fantasy-forge

# Run with included demo world
./dnd_exec data/game.dndconf
```

## Create Your Own World

1. Copy `data/game.dndconf` and edit:

```ini
[WORLD]
NAME=My Adventure
AUTHOR=Your Name

[START]
MAP=0
X=10
Y=10
GOLD=100

[MAPS]
0,maps/overworld.dndmap,OVERWORLD,The Realm
1,maps/town.dndmap,TOWN,Starting Village
```

2. Draw maps in ASCII (`maps/overworld.dndmap`):

```
NAME:The Realm
SIZE:40,25
START:10,10
TYPE:OVERWORLD
########################################
#......................................#
#..~~~~..........M.......##########...#
#..~~~~...................#........#..#
#........................>#........#..#
#..........M.............##########...#
#......................................#
########################################
```

3. Run: `./dnd_exec data/myworld.dndconf`

## File Formats

| Extension | Purpose | Format |
|-----------|---------|--------|
| `.dndconf` | World configuration | INI-style |
| `.dndmap` | Map/level layouts | ASCII grid + header |
| `.dnddat` | Data tables (items, monsters, classes) | CSV |
| `.dndscript` | NPC dialogue & events | Custom scripting |
| `.dndsav` | Save games | INI-style with checksum |

### Map Tiles

| Char | Meaning | Char | Meaning |
|------|---------|------|---------|
| `#` | Wall | `+` | Closed door |
| `.` | Floor | `'` | Open door |
| `~` | Water | `X` | Locked door |
| `<` | Stairs up | `>` | Stairs down |
| `M` | Monster spawn | `N` | NPC spawn |
| `*` | Chest | `$` | Gold |
| `S` | Shop | `I` | Inn |

### Data Tables

**Items** (`items.dnddat`):
```csv
ID,NAME,SYMBOL,TYPE,VALUE,EFFECT,PARAM
1,Short Sword,),WEAPON,10,DAMAGE,1d6
9,Healing Potion,!,POTION,25,HEAL,2d4
```

**Monsters** (`monsters.dnddat`):
```csv
ID,NAME,SYMBOL,HP,AC,DAMAGE,XP,LEVEL
1,Goblin,g,8,12,1d6,25,1
3,Troll,T,30,15,2d6+2,100,4
```

**Classes** (`classes.dnddat`):
```csv
ID,NAME,HP,MP,STR,DEX,CON,INT,WIS,CHA
1,Warrior,30,5,12,8,12,6,8,8
2,Mage,18,25,6,8,6,14,12,10
```

## Project Structure

```
Fantasy-Forge-Engine-/
└── dnd/
    └── ailang/
        ├── dnd.ailang              # Main entry point
        ├── data/
        │   ├── game.dndconf        # Demo world config
        │   ├── items.dnddat
        │   ├── monsters.dnddat
        │   ├── classes.dnddat
        │   ├── shops.dnddat
        │   └── encounters.dnddat
        ├── maps/
        │   ├── overworld.dndmap
        │   ├── town.dndmap
        │   └── dungeon1.dndmap
        ├── saves/
        └── docs/
        |   ├── DATA_FORMATS.md
        |   ├── SAVE_SYSTEM.md
        |   └── NETWORK_ARCHITECTURE.md
        |
        ├── sprites/
        │   ├── default/
        │   │   ├── tileset.png        # 16x16 or 32x32 grid sprite sheet
        │   │   ├── tileset.json       # Metadata mapping
        │   │   └── preview.png        # Optional preview image
        │   ├── kenney/                # Alternative theme
        │   │   ├── tileset.png
        │   │   └── tileset.json
        │   └── sprites.md             # Documentation
        |
        |___Librarys_
                    ├── Library.DND.ailang      # Core game engine
                    ├── Library.TUI.ailang      # Terminal rendering
                    ├── Library.DICE.ailang     # RNG and dice rolls
                    ├── Library.Character.ailang
                    ├── Library.Item.ailang
                    ├── Library.Shop.ailang
                    ├── Library.Inn.ailang
                    ├── Library.Save.ailang
                    ├── Library.SaveScreen.ailang
                    ├── Library.World.ailang
                    ├── Library.GameConfig.ailang
                    ├── Library.Encounter.ailang
                    ├── Library.BattleScreen.ailang
                    ├── Library.EquipScreen.ailang    

```

## Roadmap

### Current (v0.1)
- [x] Core engine (maps, combat, progression)
- [x] Shops and inns
- [x] Save/load with party support
- [x] Random encounters
- [x] Equipment system

### Phase 2: NPCs & Story
- [ ] NPC spawns and movement
- [ ] Dialogue script parser
- [ ] Quest system with flags

### Phase 3: Multiplayer
- [ ] Telnet server (MUD-style)
- [ ] Shared world mode
- [ ] Party system (co-op)
- [ ] DM mode for live campaigns

### Phase 4: Polish
- [ ] Browser rendering (WebSocket + rot.js)
- [ ] Sprite theming system
- [ ] Sound effects

## Performance

| Metric | Target | Current |
|--------|--------|---------|
| Binary size | < 250KB, could grow to 1mb or so with full features | ~221KB current binary  |
| Idle CPU | < 1% | ~0% |
| Memory (typical world) | < 1MB, will grow with larger map files| 500kb current ram useage full game engine running with 4 maps loaded  |
| Map rendering | 60+ FPS | ✓ |

Tested stable for 1+ hours continuous play with no memory growth.

## Contributing

Contributions welcome! Please:

1. Open an issue first for major changes
2. Focus on data files over code changes when possible
3. Test with existing saves
4. Document new file formats

See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

## Documentation

- [Developer Onboarding Guide](ONBOARDING.md) — Quick start, architecture, common tasks
- [Data Formats & Modding Guide](DATA_FORMATS.md) — File specs for maps, items, monsters, etc.
- [Library API Reference](LIBRARY_REFERENCE.md) — Full function reference for all libraries
- [Save System](SAVE_SYSTEM.md) — Save/load, quest flags, checkpoints
- [Network Architecture](NETWORK_ARCHITECTURE.md) — Multiplayer design (planned)
- [Project Roadmap](PROJECT_ROADMAP.md) — Current status and future plans

## License

MIT License — use it, modify it, share it. See [LICENSE](LICENSE).

## Credits

Built with [AILang](https://github.com/AiLang-Author/AiLang) — a systems programming language that compiles to native x86-64.

---

*"The goal is a truly open D&D platform for community mods and shared worlds."*
