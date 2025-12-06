# Fantasy Forge Engine - Sprite System

## Overview

The sprite system maps ASCII characters to graphical tiles for browser-based rendering. The engine outputs JSON game state, which a web client renders using sprite sheets.

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│   Engine    │────►│    JSON     │────►│   Browser   │
│  (AILang)   │     │   Output    │     │  (rot.js)   │
└─────────────┘     └─────────────┘     └─────────────┘
                                              │
                                              ▼
                                        ┌─────────────┐
                                        │ tileset.png │
                                        │ tileset.json│
                                        └─────────────┘
```

---

## Directory Structure

```
sprites/
├── default/                 # Default theme
│   ├── tileset.png         # Sprite sheet image
│   ├── tileset.json        # Tile mapping metadata
│   └── preview.png         # Optional theme preview
├── retro/                   # Alternative theme
│   ├── tileset.png
│   └── tileset.json
├── kenney/                  # Community theme
│   ├── tileset.png
│   └── tileset.json
└── README.md               # This file
```

---

## Sprite Sheet Specifications

### Image Format

| Property | Requirement |
|----------|-------------|
| Format | PNG (transparency supported) |
| Color Depth | 32-bit RGBA |
| Tile Size | 16x16 or 32x32 (consistent) |
| Layout | Grid, no spacing |
| Background | Transparent |

### Recommended Sizes

| Tile Size | Sheet Size | Tiles | Best For |
|-----------|------------|-------|----------|
| 16x16 | 256x256 | 256 | Classic retro, fast loading |
| 32x32 | 512x512 | 256 | More detail, modern retro |
| 16x16 | 512x256 | 512 | Extended tile sets |

### Grid Layout

```
       Col 0   Col 1   Col 2   Col 3   Col 4   ...
      ┌───────┬───────┬───────┬───────┬───────┬───
Row 0 │ wall  │ floor │ door  │ door  │ locked│ ...  ← Terrain
      │  #    │  .    │  +    │  '    │  X    │
      ├───────┼───────┼───────┼───────┼───────┼───
Row 1 │stairs │stairs │ water │ trap  │ chest │ ...  ← Terrain cont.
      │  <    │  >    │  ~    │  ^    │  *    │
      ├───────┼───────┼───────┼───────┼───────┼───
Row 2 │player │ shop  │ inn   │portal │ gold  │ ...  ← Objects
      │  @    │  S    │  I    │  O    │  $    │
      ├───────┼───────┼───────┼───────┼───────┼───
Row 3 │goblin │ orc   │ troll │ slime │ skel  │ ...  ← Monsters
      │  g    │  o    │  T    │  s    │  k    │
      ├───────┼───────┼───────┼───────┼───────┼───
Row 4 │ king  │ guard │villgr │mercht │ sage  │ ...  ← NPCs
      │  K    │  G    │  V    │  M    │  W    │
      ├───────┼───────┼───────┼───────┼───────┼───
Row 5 │warrior│ mage  │ rogue │cleric │ranger │ ...  ← Player Classes
      ├───────┼───────┼───────┼───────┼───────┼───
Row 6 │ sword │ armor │shield │helmet │ ring  │ ...  ← Items
      ├───────┼───────┼───────┼───────┼───────┼───
Row 7 │potion │potion │scroll │ key   │ food  │ ...  ← Consumables
      │ (red) │(blue) │       │       │       │
      ├───────┼───────┼───────┼───────┼───────┼───
Row 8 │ hit   │ miss  │ heal  │ fire  │ ice   │ ...  ← Effects
      └───────┴───────┴───────┴───────┴───────┴───
```

---

## tileset.json Format

The JSON file maps ASCII characters and entity IDs to sprite positions.

```json
{
  "name": "Theme Name",
  "version": "1.0",
  "author": "Author Name",
  "license": "CC0 / MIT / etc",
  "tileWidth": 16,
  "tileHeight": 16,
  "columns": 16,
  "rows": 16,
  "mapping": {
    "tiles": { ... },
    "monsters": { ... },
    "npcs": { ... },
    "classes": { ... },
    "items": { ... },
    "effects": { ... }
  }
}
```

### Mapping Entry Format

```json
"#": { 
  "x": 0,           // Column in sprite sheet
  "y": 0,           // Row in sprite sheet
  "name": "wall",   // Human-readable name
  "animated": false // Optional: has animation frames
}
```

### Animated Tiles

For animated tiles, specify frame count and speed:

```json
"~": {
  "x": 2,
  "y": 1,
  "name": "water",
  "animated": true,
  "frames": 4,       // 4 frames horizontally
  "speed": 200       // ms per frame
}
```

Frames are laid out horizontally from the start position:
```
[frame0][frame1][frame2][frame3]
```

---

## Using Sprites in Browser Client

### rot.js Example

```javascript
// Load tileset
const tileSet = document.createElement("img");
tileSet.src = "sprites/default/tileset.png";

// Load mapping
const mapping = await fetch("sprites/default/tileset.json")
  .then(r => r.json());

// Create display
const display = new ROT.Display({
  width: 80,
  height: 25,
  layout: "tile",
  tileWidth: mapping.tileWidth,
  tileHeight: mapping.tileHeight,
  tileSet: tileSet,
  tileMap: buildTileMap(mapping)
});

// Build tile map from our format
function buildTileMap(mapping) {
  const map = {};
  const tw = mapping.tileWidth;
  const th = mapping.tileHeight;
  
  // Map ASCII chars
  for (const [char, data] of Object.entries(mapping.mapping.tiles)) {
    map[char] = [data.x * tw, data.y * th];
  }
  
  // Map monsters
  for (const [char, data] of Object.entries(mapping.mapping.monsters)) {
    map[char] = [data.x * tw, data.y * th];
  }
  
  return map;
}

// Draw a tile
display.draw(x, y, "#");  // Draws wall sprite
```

### Canvas Example

```javascript
function drawTile(ctx, char, screenX, screenY) {
  const tile = mapping.mapping.tiles[char] 
            || mapping.mapping.monsters[char]
            || mapping.mapping.npcs[char];
  
  if (tile) {
    ctx.drawImage(
      tileSet,
      tile.x * tileWidth,    // Source X
      tile.y * tileHeight,   // Source Y
      tileWidth,             // Source width
      tileHeight,            // Source height
      screenX,               // Dest X
      screenY,               // Dest Y
      tileWidth,             // Dest width
      tileHeight             // Dest height
    );
  }
}
```

---

## Creating a Theme

### Step 1: Create Directory

```bash
mkdir sprites/mytheme
```

### Step 2: Create Sprite Sheet

Use any pixel art tool:
- **Aseprite** (paid, excellent)
- **LibreSprite** (free Aseprite fork)
- **Piskel** (free, web-based)
- **GIMP** (free, general purpose)

### Step 3: Follow Grid Layout

- Each tile must be exactly `tileWidth` × `tileHeight`
- No spacing between tiles
- Transparent background for non-rectangular sprites

### Step 4: Create tileset.json

Copy `default/tileset.json` and update coordinates as needed.

### Step 5: Test

Load your theme in the browser client and verify all tiles display correctly.

---

## Required Tiles

At minimum, a theme must include:

### Terrain (Required)
| Char | Name | Description |
|------|------|-------------|
| `#` | wall | Impassable wall |
| `.` | floor | Basic walkable floor |
| `+` | door_closed | Closed door |
| `'` | door_open | Open door |
| `<` | stairs_up | Ascending stairs |
| `>` | stairs_down | Descending stairs |
| `~` | water | Water (impassable) |

### Objects (Required)
| Char | Name | Description |
|------|------|-------------|
| `@` | player | Player character |
| `*` | chest | Treasure chest |
| `$` | gold | Gold pickup |
| `S` | shop | Shop location |
| `I` | inn | Inn location |

### Monsters (Recommended)
| Char | Name | ID |
|------|------|-----|
| `g` | goblin | 1 |
| `o` | orc | 2 |
| `T` | troll | 3 |
| `s` | slime | 10 |
| `k` | skeleton | 11 |
| `D` | dragon | 20 |

---

## Theme Distribution

### Licensing

If distributing themes:
- Original art: specify your license (MIT, CC0, etc.)
- Based on existing assets: respect original license
- LPC assets: typically CC-BY-SA or CC-BY

### Recommended Sources for Assets

| Source | License | Style |
|--------|---------|-------|
| [OpenGameArt.org](https://opengameart.org) | Various | Mixed |
| [Kenney.nl](https://kenney.nl) | CC0 | Clean, modern |
| [LPC](https://lpc.opengameart.org) | CC-BY-SA | RPG-focused |
| [itch.io](https://itch.io/game-assets/free) | Various | Indie |

---

## Future Features

- [ ] Animation support in browser client
- [ ] Per-map theme overrides
- [ ] Layered tiles (floor + object)
- [ ] Character customization (hair, armor overlays)
- [ ] Day/night palette swaps
- [ ] Weather overlays (rain, snow particles)