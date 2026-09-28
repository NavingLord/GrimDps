# GrimDps

An in-game **damage / DPS overlay** for *Grim Dawn* (64-bit).

GrimDps shows your damage and DPS statistics inside the game window itself.

It ships as two pieces: **`GrimDps.exe`** (the loader: process detection, injection and the
settings UI) and **`GrimDps.dll`** (the overlay that gets injected into the game).

---

## Requirements

- *Grim Dawn*, **64-bit** build — "Grim Dawn (x64)", which is the default first launch option
- Administrator rights: the loader injects a DLL into the game process, so it asks for
  elevation (UAC) once
- The legacy **x86 / DX9** launch options are **not** supported (the overlay hooks D3D11)

---

## Quick start

1. Double-click **`GrimDps.exe`** and accept the UAC prompt.
2. The loader looks for *Grim Dawn* and injects automatically. Start order does not matter:
   it monitors the process and injects as soon as the game starts ("auto-inject on game
   start" is on by default). "Inject now" and "Unload" are there for manual control.
3. The overlay appears inside the game.
4. In game press **F8** to enter drag mode: the panels get a title bar and can be moved with
   the mouse. Press **F8** again to leave drag mode — the new positions are saved.
5. Press **F7** to reset the statistics and start a new measurement.

Both hotkeys are rebindable in the loader ("Hotkeys"), and outside drag mode the overlay
ignores the mouse completely, so it never steals a click from the game.

---

## Features

1. **Damage shares as a pie chart or a live colour bar.**
2. **Display precision**: the number of decimals for every percentage, and the number
   format — `Plain (675000)`, `Metric (675 K)` or `Wan (67.5万)`.
3. **Each region can be shown or hidden** independently.
4. **Per-region appearance**: font, font size, title / border / label / value colours, and
   toggles for the pie chart, the share bar, the damage-type shares and "No border"; for
   *Resistances & OA/DA* there is also a horizontal layout with a configurable number of
   rows. Click a region name in the loader to edit that region.
5. **Combat Overview: choose which lines are shown** (that region → *Advanced*), so the
   panel can be as compact or as detailed as you like.
6. **Hide the noise**: hide damage sources and damage types below x %, and optionally merge
   everything below x % into "Other".
7. **Automatic death records**: when your character dies, the damage taken during the last
   **8 seconds** is captured and written as an HTML report into `deathRecords/` (the
   loader's *Death Records* tab lists them and opens them for review).
8. **Fullscreen and windowed** both work.

The regions are *Combat Overview, Skill Damage, Pet Damage, Pet Damage Taken, Damage Taken,
Healing & Absorption, Resistances & OA/DA* and *Buffs & Debuffs*, plus a nemesis progress
tracker.

The design intent behind all of this is simple: the overlay should not get in the way of
playing. How much information it shows, where it sits and how it looks are all meant to be
under your control — which also makes it handy for studying game mechanics and measuring a
build's output.

---

## Configuration

Everything is remembered in **`GrimDps.cfg`**, a plain-text file next to the loader and the
DLL. It is hot-reloaded: the overlay picks up a change (including a hand edit) while the
game is running — no restart, no re-injection.

Because your layout and preferences live in that file, you can keep your own `GrimDps.cfg`
across updates instead of readjusting everything. Panel positions are stored per region, so
a saved layout stays exactly where you put it.

The file is line oriented and readable; a few of the keys (the file itself carries all of
them):

```ini
lang=zh                  # UI language: en es fr de it cs ja ko pl pt ru vi zh
fontFile=msyh.ttc        # default font: file name, family name or path
fontSize=16              # default font size
numFormat=1              # 0 Plain (675000), 1 Metric (675 K), 2 Wan (67.5万)
dmgPctDecimals=1         # decimals used for every percentage
panel.totals=1,1,1498,12 # region shown + its pinned position (drag mode writes this)
font.skills=simhei.ttf   # per-region font (empty = follow the default)
fsize.skills=18          # per-region font size (0 = follow the default)
pie.skills=1             # pie chart for this region
shareBar.skills=1        # colour bar for this region
hideBorder.skills=1      # draw no border on this region
showTypes.skills=1       # damage-type shares for this region
totalShare.skills=1      # show the total damage share (Skill Damage)
mergeBelow.skills=1      # merge sources below x % into "Other"
hideBelowPct=0           # hide sources / types below x %
```

---

## Fonts

You can pick any installed font, globally and per region. A font may be given as a file name
(`msyh.ttc`), a family name (`Microsoft YaHei`) or a path; the overlay resolves it by
searching, in order, a `fonts` folder next to the DLL, the game folder, the Windows font
folder (wherever Windows is installed), your per-user font folder and — on Proton/Linux —
the host's `/usr/share/fonts`.

Drop a `.ttf` / `.ttc` / `.otf` into that `fonts` folder to use a font without installing it
system-wide.

---

## Languages

13 UI languages ship with the plugin: English, Spanish, French, German, Italian, Czech,
Japanese, Korean, Polish, Portuguese, Russian, Vietnamese and Chinese. The glyph set covers
all of them, so switching the language needs neither a restart nor a re-injection.

---

## Notes

- Local only: the plugin does not modify game files and does not touch the network. The
  only files it writes are its own (`GrimDps.cfg`, `GrimDps.log`, `deathRecords/`).
- Damage numbers come from the game's own combat-text callbacks, so they match the floating
  damage text you see in game.
- The overlay is built to survive GPU device resets, alt-tab and resolution changes: it
  suspends itself and rebuilds its resources instead of taking the game down with it.

---

## SteamOS / Linux (experimental)

Grim Dawn on SteamOS/Linux runs the Windows build through Proton, so the loader has to run
inside the same Proton prefix. `linux/run-grimdps.sh --check` reports what it found, and
running it without arguments starts the loader in that prefix. The in-game overlay under
Proton/DXVK has **not** been verified yet.

---

## Troubleshooting

- **"Grim Dawn process not found"** — start the game (the loader also waits for it) and make
  sure you are using the 64-bit launch option.
- **Nothing appears in game** — the overlay needs the D3D11 path, so do not use the legacy
  x86/DX9 launch option. Check `GrimDps.log` next to the DLL, and whitelist the folder in
  your antivirus (remote-thread injection is routinely flagged).
- **Text shows as `?`** — the selected font has no glyphs for that language. Pick a font
  with wider coverage (for example Microsoft YaHei for Chinese/Japanese, Malgun Gothic for
  Korean) or drop a suitable font into the `fonts` folder.
- **The panel is too small or too large** — change the font size: per region, or the global
  default in the loader.

---

## Folder layout

```
GrimDps.exe        loader / injector and settings UI
GrimDps.dll        the injected overlay
GrimDps.cfg        every setting (plain text, hot-reloaded)
GrimDps.log        debug log (next to the DLL)
fonts/             optional extra fonts for the overlay
assets/, bg.jpg    preview backdrops used by the loader
deathRecords/      generated death reports (HTML)
linux/             run-grimdps.sh for SteamOS / Linux (Proton)
```


