# /six

A fast, modern Minecraft: Java Edition launcher with a purple / white / black gradient look — plus a real in-game Fabric client that is **not** a cheat client.

Built with Electron + plain HTML/CSS/JS — no bundler, no frontend framework.

## Features

- **Every Minecraft version** — the full Mojang release manifest and the full snapshot manifest are both selectable, newest first, with a Release / All filter and search.
- **Vanilla + Fabric** — create a vanilla instance or let six install the matching Fabric loader for the version you picked.
- **Minecraft-native look** — Monocraft throughout, 14 accent swatches plus a custom colour picker, a blocky voxel-ridge backdrop that reacts to the pointer, a `/six` opening animation, and a Motion switch that freezes all of it. The in-game accent stays separate from the launcher accent.
- **six Client included** — auto-installed on new Fabric instances (with a Settings switch to turn that off): ClickGUI, HUD editor, 70 modules, keybinds, zoom, coordinate copy and launcher-synced theming.
- **Frame pack** — one click installs Sodium, Lithium, FerriteCore, Krypton, ImmediatelyFast, EntityCulling, ModernFix, MoreCulling and BadOptimizations for the instance's exact version, plus tuned G1GC flags at launch.
- **Command palette** — press `Ctrl+K` to jump anywhere, launch an instance, install the client or change the accent.
- **Accounts** — a proper account switcher: avatar grid, search, sort by date added, active checkmark, add-account card, per-account skins and a `...` menu with rename / remove / log out. Microsoft sign-in uses the device-code flow, offline accounts work with no sign-in at all.
- **Automatic Java** — detects installed runtimes (including the official launcher's runtime) and downloads a matching Temurin JRE when needed.
- **Full game downloads** — Mojang version manifest, client jar, libraries, natives and assets, with live progress and checksum checks.
- **Modrinth browser** — search mods for your exact Minecraft version, install with one click, and required dependencies come along.
- **Local mod manager** — drag in `.jar` files, enable/disable them, delete them.
- **Performance options** — memory slider, Aikar's G1GC flags, custom JVM arguments.
- **Updates** — the build carries its own feed, so an installed copy checks on start and upgrades itself in place. Settings → Updates shows the current build, install mode, auto-update switch and live download progress; portable copies swap their exe, installed copies run the next setup silently.
- **Extras** — custom resolution, close-launcher-on-play, per-version console log, Mojang status indicator.

## six Client

The in-game client is a real Fabric mod, bundled at `resources/six-client.jar` and installed into an instance's `mods/` folder. Two builds ship in `resources/clients` and the launcher picks by version:

| Build | Minecraft |
| --- | --- |
| `six-client-1.21.1.jar` | 1.21.x |
| `six-client-26.1.jar` | 26.1.x |

Configure it on the **Client** page; the launcher and the game share `game/config/six-client.json`.

| Key | Action |
| --- | --- |
| `Right Shift` | Open / close the ClickGUI |
| `C` | Smooth zoom |
| `F6` | Copy coordinates |
| `Right Alt` | Toggle the HUD |

The ClickGUI is a compact floating panel (roughly 70% of the viewport, capped so it stays a window on 4K), with module search, HUD / Utility / Client tabs, per-module keybinds, accent swatches, HUD scale, zoom amount and blur / gradient / shadow / animation toggles. **Right-click any module** for its own options sheet — a centered modal with that module's toggles, choices and sliders. The HUD editor below it has drag, snap, grid, arrow-key nudging and reset.

70 modules ship in the bundle: 44 HUD readouts (FPS, CPS, TPS, ping + ping graph, coordinates, biome, level, hunger, health, armor status, potion effects, target, entities, light, weather, keystrokes, session, speed, direction, portal coords, spawn distance, distance walked, inventory, offhand, waypoints, active module list and more) and 26 utility modules (zoom, copy coordinates, toggle HUD, chat, tab list, fullbright, auto sprint, toggle sneak, cosmetics, skins, notifications, durability warning, quick messages, time changer, no view bobbing, nametags, tab ping, block overlay, hitboxes, remove hurt cam, hide boss bar, particles, crosshair, scoreboard, chat timestamps, auto gg).

Every Client-page control writes the same `game/config/six-client.json` the mod polls every 500 ms, so accent, HUD scale, zoom, transitions and module switches apply in game without a restart. Enabling a module from the launcher re-flows the HUD columns on the next frame; positions are only frozen once something is dragged in the HUD editor.

Everything is quality of life: no combat, movement, reach or exploit modules.

### HUD

- **Armor status** shows the four equipped pieces as item icons with durability, stacked icon-over-percentage, and can be laid out horizontally or vertically.
- **Custom crosshair** is a module option: dot, cross, or a purple ring, with size and gap controls.
- Positions, scale and per-module settings survive restarts because they live in the shared config file.

### Cosmetics

The Client page ships six capes, six elytra skins and six name tags. Pick one under **Cosmetics**; the launcher writes it to `six-client.json` and the mod swaps the cape/elytra texture on the local player while keeping vanilla geometry, animation and lighting.

The cape and elytra use the full `six-logo.png` art — the chrome wordmark, its background and the purple trim — with a symmetric wing plate, chrome ribs, a dark gutter and an ember tip on the elytra, so the model reads as a wing rather than a flat rectangle.

The `six` tag renders as `<username> /SIX` above the wearer's head: the name in white, the `/SIX` mark in a purple-to-white gradient, in the same slot and at the same scale as a vanilla nametag. It renders in third person (F5) because vanilla never draws a nametag for the camera's own entity, so the mod hooks the entity dispatcher rather than the label renderer. Cosmetics are client-local by design — they are your look on your screen, not a server-side reward.

### Building the client

```bash
cd six-client
..\tools\gradle-8.12\bin\gradle.bat build
copy build\libs\six-client-1.0.0.jar ..\resources\six-client.jar
copy build\libs\six-client-1.0.0.jar ..\resources\clients\six-client-1.21.1.jar
```

The 26.x build lives in `six-client-modern` (JDK 25, Gradle 9.8, unobfuscated official names) and produces `resources/clients/six-client-26.1.jar`. The first build of either downloads Minecraft, mappings and Fabric Loom.

## Run it

Requirements: Windows 10/11, Node.js 18+ and an internet connection for the first launch.

```bash
npm install
npm start
```

## Ship it

The update base lives in `release.config.json` and is baked into the app, so every copy you share already knows where its updates live:

```json
{
  "updateBase": "https://github.com/2fazx/si-launcher/releases/latest/download"
}
```

Build with `npm run release`. `SIX_UPDATE_BASE` overrides the configured base for a one-off build:

```powershell
$env:SIX_UPDATE_BASE="https://host/six"
npm run release
```

This produces:

| File | Purpose |
| --- | --- |
| `dist/six-setup-1.1.0.exe` | The one file to share. Installs six per-user and updates itself. |
| `dist/six-portable-1.1.0.exe` | Optional no-install build. Also updates itself. |
| `dist/latest.json` | The feed both builds read: version, URLs, sizes, SHA-256 and notes. |

Host all three files in the same folder. Share **only the setup link** (or the setup exe itself) — recipients do not need `latest.json` or the portable build. With `SIX_UPDATE_BASE` set, `latest.json` uses absolute URLs and the feed is embedded at `resources/build-config.json` inside the app.

A build made without `SIX_UPDATE_BASE` is still a normal one-file installer, but it has no baked feed: recipients would have to paste a `latest.json` URL into **Settings → Updates** once. Set the base and rebuild before sharing if it should update itself out of the box.

How the update lands:

- **Installed (setup)** — Settings → Updates checks the embedded feed. Auto-update is on by default; a new build downloads, verifies SHA-256, then a helper waits for six to exit, runs the next setup with `/S` into the same folder and relaunches it.
- **Portable** — the helper waits for six to exit, replaces the kept exe and starts it again.
- **Manual** — turn auto-update off and six offers the build with **Install update**; the progress bar reports the download live.

`npm run test:update` exercises the feed parser, version comparison, that both install modes carry the right URL and hash, and checksum rejection against a local HTTP server.

## Look and feel

Everything is in **Settings → Appearance**:

- **Accent colour** — 14 swatches plus the native colour picker. One value themes the launcher: nav highlight, buttons, toggles, switches, focus rings, backdrop towers and the intro glow. In-game theming stays in **Client → Appearance**.
- **Motion** — when off, hovers, view transitions, the intro and the animated backdrop stop; the canvas renders one static frame.
- **Opening animation** — the `/six` boot card with its loading bar. Disable it here; it also respects `prefers-reduced-motion`.
- **Backdrop** — canvas, not an image: stepped voxel ridges in teal, pink, gold and the current accent, glowing block towers, floating voxels, drifting stars and slow aurora ribbons. It is capped at 30 FPS, pauses when the window is hidden and never uses more than 1.35× device pixel ratio.

## Microsoft sign-in

Offline accounts work immediately. Microsoft sign-in uses a bundled public client ID (the same one the official launcher flow uses) with the device-code flow, so there is no Azure registration to do:

1. **Settings → Account → Sign in with Microsoft**.
2. Open the link it shows and enter the device code.
3. Approve the prompt; the launcher refreshes the session automatically from then on.

If you would rather use your own Azure app, paste its client ID into **Settings → Account → Microsoft client ID** and it takes priority over the bundled one.

The account must own Minecraft: Java Edition and have an Xbox profile.

## Data

Everything lives in:

```
%APPDATA%\six-launcher
```

- `instances/` — game folders (worlds, mods, options)
- `versions/`, `libraries/`, `assets/` — shared Minecraft files
- `java/` — runtimes downloaded by six
- `settings.json`, `account.json` — launcher state

## Notes

- Vanilla and Fabric are supported. Forge/NeoForge are not installed yet.
- The launcher targets Windows (paths and natives handling are Windows-specific).
- Not affiliated with Mojang Studios or Microsoft.
