# NS2PCSX2-9X

**PlayStation 2 emulation for Nintendo Switch 2 — Performance, compatibility and usability.**

[![Upstream SW2PS2](https://img.shields.io/badge/upstream-SW2PS2-blue)](https://github.com/Rodpad/SW2PS2)
[![License GPL-3.0+](https://img.shields.io/badge/license-GPL--3.0%2B-blue)](LICENSE)

NS2PCSX2-9X is an independent development fork of [SW2PS2 by Rodpad](https://github.com/Rodpad/SW2PS2), powered by OunceSX2 and the ARMSX2 / PCSX2 lineage.

> **Development preview:** This repository currently contains the upstream baseline. Our experimental enhancements are being developed separately and have not yet been migrated into this fork. No stable NS2PCSX2-9X release is available yet.

## Development roadmap

| Area | Status |
| --- | --- |
| Nintendo Switch 2 build pipeline | Experimental build verified in development workspace; migration pending |
| Benchmark overlay and CPU tuning | Development builds compiled; migration pending |
| Vulkan readback optimization | Development build compiled; hardware validation pending |
| Per-game performance profiles and EE cycle rates | Development build compiled; migration pending |
| FPS patch support | Experimental; runtime verification pending |
| Advanced game library | Development in progress |
| Modern interface, graphics and recompiler improvements | Planned |

## Downloads

Official NS2PCSX2-9X builds will be published on the [Releases page](https://github.com/blueshockz47/NS2PCSX2-9X/releases) after migration and validation. Until then, this fork is not a separate stable distribution.

## Installation and compatibility

The existing upstream SD card layout is retained:

```text
/bios/ps2/          Your own PS2 BIOS
/roms/ps2/          Your own PS2 games
/switch/sw2ps2/    Emulator data, settings and saves
```

Back up your existing saves and settings before experimenting with new builds. No BIOS or games are distributed.

## Upstream documentation

The following sections describe the inherited SW2PS2 baseline and its original features. They are retained for accurate technical reference and attribution.

> **Early development:** SW2PS2 is new and there will be bugs. Please report them in
> [Issues](https://github.com/Rodpad/SW2PS2/issues) with the game and what happened. If you can, turn on
> Settings -> App -> Debug logging (shown with Advanced options), make it happen again and attach
> `/switch/sw2ps2/log.txt` from the SD card.

A PlayStation 2 emulator for the Nintendo Switch 2 (hbmenu / kaiHEN), built on the
[OunceSX2](https://github.com/nyathea/OunceSX2) core (ARMSX2 / PCSX2 lineage) with its own
native front end.

No games, BIOS files or other copyrighted material are included. You need your own PS2
BIOS (default folder `/bios/ps2`) and your own game dumps (default folder `/roms/ps2`); both
folders can be changed in Settings -> App. The app keeps its own files in `/switch/sw2ps2`
(save states in `states/`, per-game settings in `settings/`).

## Features

### Games
- **Game list** with All / Favourites / Recent tabs (L / R). Games are found in the games
  folder and any folders inside it: `.iso`, `.chd`, `.cso`, `.zso`, `.ciso`, `.gz`, `.m3u`, and `.cue` with its
  `.bin`. The BIOS is found the same way, in its folder or folders inside it.
- **Per-game settings:** a setting changed in a game's menu is kept for that game; Settings holds
  the defaults for all games.
- **Built-in speed settings** for demanding games, measured on the Switch 2 or taken from the
  community AetherSX2/NetherSX2 lists, so most games start well without tuning.

### In-game menu
Opened with L + R + L3 + R3 (SL + SR + stick press on a single Joy-Con), in tabs:
- **Game:** resume, save and load state (several slots, with optional auto-load when a game
  starts), save screenshot, reset, quit.
- **Emulation**, applied straight away: PS2 CPU speed and cycle skip, GPU readbacks
  (Accurate / Fast), Switch 2 CPU clock, internal resolution (1x and up), widescreen patches,
  and a stats overlay (FPS, full statistics, or with a frame-time graph).
- **Picture:** aspect ratio (4:3, 16:9, stretch, 1:1), integer or fit scaling, overscan cropping,
  shaders (sharp bilinear, Scale2x, LCD grid, and CRT shaders: CRT-Geom, CRT-Lottes, zfast CRT,
  CRT-Easymode, CRT-Aperture, CRT-Royale), signal look (RGB, composite, composite with dot
  crawl, S-Video), brightness boost, screen curvature, rounded corners and vignette.
- **Controls:** see below.

The menu can pause the game and dim it behind (both optional). Less common options appear with
Settings -> App -> Advanced options.

### Display and sound
- 60 Hz, or 120 Hz with VRR on displays that support it; adjustable frame delay.
- Volume, audio buffer size and controller vibration strength.

## Controllers

- **Up to four players.** Players 1 and 2 use the PS2's two controller ports. For games made
  for the PS2 Multitap (TimeSplitters: Future Perfect, Tekken Tag Tournament's Pair Play and
  others), turn on **Multitap (players 3-4)** in the in-game menu's Controls tab (saved for that
  game) or in Settings -> Sound & controls (the default for all games). Players 1-4 are then the
  Multitap's four slots, as on a real PS2. Some games need each player to press Start to join.
- **Pairing.** "Pair controllers" opens the Switch's own controller screen, with four slots,
  from Settings or mid-game. Every connected player can move around SW2PS2's menus.
- **Button remapping.** Each player's PS2 buttons can be mapped one by one on an interactive
  screen: press the Switch button you want for each PS2 button, or wait to leave it unmapped.
  Stick directions can be mapped too (for example, the stick as the D-pad). Layouts made in
  Settings apply to every game; layouts made from the in-game Controls tab apply only to that
  game. "Reset buttons to standard" puts everyone back.
- **Single Joy-Cons (experimental).** A Joy-Con held sideways works as its own controller: the
  stick and buttons are turned to match, menus show position-based button hints, and its
  default layout is SL / SR = L1 / R1, stick press = L3, - or + = Start, the four buttons the
  PS2's by position, and the stick as both analog stick and D-pad. SL + SR + stick press opens
  the in-game menu (L + R + L3 + R3 on other controllers). Games needing L2 / R2 / Select / R3
  need a remap or a fuller controller.

## SW2PS2 core improvements

SW2PS2 uses the [OunceSX2](https://github.com/nyathea/OunceSX2) core with its own changes
(`patches/ouncesx2.patch`).

### Performance

- **Smarter detection of self-modifying code.** Under kaiHEN there is no hardware memory
  protection, so the core otherwise re-checks every compiled block of game code each time it
  runs. SW2PS2 tracks writes to the memory pages that hold compiled code instead, including DMA
  transfers, and recompiles only code that was actually overwritten.
- **ARMSX2's newer IOP thread, made to work on the Switch 2.** The PS2's I/O processor runs on
  its own core using ARMSX2's latest IOP thread, including its lag control. SW2PS2 adds the
  Switch 2 fixes it needs: detecting the console's CPU cores correctly (the standard check
  reports none) and waiting without WFE instructions, which never wake on the Switch 2.
- **Per-game speed settings built in.** Demanding games start with emulated-CPU settings
  measured on the Switch 2, falling back to the community-tested AetherSX2/NetherSX2 lists. Any
  game can be adjusted from the in-game menu, and the app tells you once if a game falls below
  full speed.

### Stability

- **Several games in one session.** Starting a second game and loading a state no longer
  crashes. The cause was the core's background VU compiler, which SW2PS2 turns off; it gave no
  measurable speed.
- **Relaunching from hbmenu.** The emulated PS2's memory is reserved once and released when the
  app exits; before, repeated use fragmented memory until the app could no longer start.
- **Loading a state right after boot.** A state load is held until the game itself has
  started; loading while the BIOS was still booting crashed God of War II.

## Layout

| Path | What |
|---|---|
| `source/` | the front end (game list, menus, settings, video/audio output, test harness) |
| `native/` | glue between the front end and the core (libretro bridge, Horizon helpers; two files of OunceSX2's own app compiled in) |
| `patches/` | SW2PS2's changes to the emulator cores, against the upstream commits in `patches/upstreams.txt` |
| `romfs/` | fonts (with their licences) and picture shaders |
| `icon.png`, `sw2ps2.jpg` | the app icon: the lossless master and the JPEG the build uses (`sw2ps2-test.jpg`: test builds) |
| `tools/` | speed-list generator, profiling helpers, third-party setup |
| `tests/` | on-console test runner (`remote_test.sh`) and its log summariser |
| `probe/` | a small hardware probe app |

## Building

Needs devkitPro (devkitA64) with the ounce-devkit Switch 2 additions (libounce, kaiHEN, OunceVK).

1. `tools/setup_third_party.sh` clones the cores at the pinned commits and applies `patches/`.
2. `tools/build_core.sh` builds libjpeg-turbo (from the core's own copy) and the core, with the
   exact settings the app is built with (CMake + Ninja).
3. `make` builds the app (the Vulkan build on the devkit's OunceVK driver).

After changing what the app links, `tools/make_notices.py` regenerates
`romfs/licenses/THIRD_PARTY_NOTICES.txt`.

## Licence

SW2PS2 is licensed under the GNU General Public License v3.0 or later (see `LICENSE`), as the
OunceSX2 / ARMSX2 / PCSX2 core it builds on is. The libraries linked into the app keep their
own licences: their notices are in `romfs/licenses/THIRD_PARTY_NOTICES.txt`, which ships inside
the app with the GPL text.

## Credits

- [Rodpad / SW2PS2](https://github.com/Rodpad/SW2PS2): original Nintendo Switch 2 frontend, integration, optimizations and upstream project.

- [OunceSX2](https://github.com/nyathea/OunceSX2), [ARMSX2](https://github.com/ARMSX2/ARMSX2) and
  [PCSX2](https://github.com/PCSX2/pcsx2): the emulator core (GPL-3.0+). From OunceSX2's Switch 2 app,
  `HorizonThreadWrap` and `ServiceLookupGuard` are compiled in, and its approach to setting Switch 2
  clock rates and to cleaning up threads at exit is followed. The IOP thread is ARMSX2's.
- Per-game CPU settings adapted (converted to SW2PS2's own table) from the community-tested
  [AetherSX2/NetherSX2 playable lists](https://github.com/Darkelf2024/PS2-Playable-Lists-for-AetherSX2-NetherSX2)
  by Darkelf2024, licensed [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
- Fonts: Roboto (Apache 2.0), Source Code Pro (OFL 1.1) — licences in `romfs/licenses`.
