# Credits and asset report

## External assets used

None yet. Every texture, prop and sound in the game is generated in code at load time.

The CC0 sources from the asset brief could not be downloaded. This build environment's network policy blocks them: the proxy answered `403` to CONNECT for every one of these hosts. Because no file was downloaded, no license page could be checked either, so nothing external is included.

| Source | URL | Author | License (per brief) | Status |
|---|---|---|---|---|
| Platformer Pack Industrial | https://opengameart.org/content/platformer-pack-industrial-100 | Kenney | CC0 (to verify on page) | Blocked (opengameart.org 403) |
| Tileable metal textures | https://opengameart.org/content/tileable-metal-textures-gratedamagedpng | nublet | CC0 (to verify on page) | Blocked (opengameart.org 403) |
| Metal grate | https://opengameart.org/content/metal-grate | GoldenThumbs | CC0 (to verify on page) | Blocked (opengameart.org 403) |
| Poly Haven textures | https://polyhaven.com | various | CC0 (to verify per asset) | Blocked (polyhaven.com 403) |
| ambientCG materials | https://ambientcg.com | various | CC0 (to verify per asset) | Blocked (ambientcg.com 403) |

To use them later, allow those domains in the cloud environment's network settings. The texture code is already built around a single pipeline (`grimify()`: desaturate to ~30%, multiply by #1a2a33, grime), so a real texture can replace a procedural one by swapping the source canvas in `buildTextures()`.

## Replaced by procedural textures (generated in code)

| What the brief asked for | Procedural replacement (in `index.html`) |
|---|---|
| nublet metal wall / Poly Haven painted metal | `paintWallPlate()`: bevelled panels, seams, rivet rows, scratches, dents, drip streaks |
| Rusty metal (engine room) | `paintRust()`: wall plating + fbm rust blooms, extra grime, redder tint |
| Corrugated / exterior hull metal | `paintHull()`: large plates, weld seams, dense rivet rows, rust patches, heavy streaks |
| GoldenThumbs metal grate / nublet damaged grate | `paintGrate()`: slotted floor grating |
| nublet treadplates / diamond plate | `paintTread()`: diamond treadplate walkway |
| nublet vent | `paintVent()`: slatted vent grille with rivets |
| Kenney hazard stripe pieces | `paintHazard()`: dim yellow/black diagonal stripes |
| Kenney pipes, girders, lamps, crates | Drawn in code: `pipe()` (shaded with flanges), structural ribs, ceiling tube fixtures, `cable()`, a crate |
| Film grain | `paintGrain()`: sparse light/dark specks, baked into pre-rendered vignette frames |

All of them go through `grimify()` (desaturate, dark teal-grey multiply, low-frequency grime), the same pipeline real CC0 textures would use.

## Size

Total external assets: 0 bytes. The whole game is the single `index.html`.
