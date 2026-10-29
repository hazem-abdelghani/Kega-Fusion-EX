![Kega Fusion EX](images/KEGA_EX_logo.png)
# Kega Fusion EX

A ready-to-run copy of **Kega Fusion v3.64** (Windows) — the Sega SG-1000 / SC-3000 / Master System / Game Gear / Mega Drive (Genesis) / Sega CD / 32X / Pico emulator by Steve Snake — repackaged as **Fusion EX**, with two drop-in wrappers and a lightly modified executable that fix the biggest annoyances on modern Windows.

| Problem with stock Kega Fusion | Fix in this package | Provided by |
| --- | --- | --- |
| Can't hold a smooth **60 FPS**, and there is a noticeable **delay when switching to full screen** | DirectDraw is translated to Direct3D 9 | [DxWrapper](https://github.com/elishacloud/dxwrapper) |
| **Left stick and D-pad can't be used together** on a gamepad | Xbox-style controllers are exposed with the D-pad *and* left stick both mapped to the X/Y axes | [Xidi](https://github.com/samuelgr/Xidi) |

No installation is needed — just extract and run `Fusion EX.exe`.

---

## Quick start

1. Extract the whole `Kega Fusion EX` folder anywhere (a normal folder, not `Program Files`, is easiest).
2. Run **`Fusion EX.exe`**.
3. Load a game from the **File** menu (Genesis / 32X, Sega CD, Master System or Game Gear).
4. Set up your controller: open the settings (**Settings…**, formerly *Set Config…*), go to the controller page, set the port to **Joystick**, and click **DEFINE** to bind your buttons. (This step can't be skipped — which controller plugs into which port, and how its buttons are mapped, is personal and can't be guessed for you.)

Keep every `.dll` and `.ini` file **in the same folder as `Fusion EX.exe`** — that's how the wrappers get loaded.

Keyboard shortcuts (save/load state, fast-forward, fullscreen toggle, render-mode switching, etc.) work out of the box — see `Readme.txt` for the full list.

---

## What was changed

### 1. 60 FPS and faster full-screen switching — DxWrapper

Kega Fusion draws through DirectDraw, which modern Windows only emulates. [DxWrapper](https://github.com/elishacloud/dxwrapper) sits in front of it and converts the DirectDraw calls to Direct3D 9, which results in smooth 60 FPS output and removes the delay when toggling full screen.

| File | Role |
| --- | --- |
| `ddraw.dll` | DxWrapper stub — Windows loads this instead of the system `ddraw.dll` |
| `dxwrapper.dll` | The actual wrapper the stub hands off to |
| `dxwrapper.ini` | Configuration |

Version: **DxWrapper 1.9.8700.25**

> **Important — turn VSync off.** The full-screen delay fix only works when **VSync is disabled** in Kega Fusion. This is already the default in this package — if you ever reset Fusion's settings, open the **Video** menu and make sure **VSync** is *unchecked* before switching to full screen.
>
> **Also match your desktop resolution.** Set the full-screen resolution to the same resolution Windows is using (**Video → Full Screen Resolution**). A different resolution makes Windows change the display mode, which brings back the delay when switching to full screen.

The settings that matter in `dxwrapper.ini`:

```ini
[Compatibility]
Dd7to9              = 1      ; DirectDraw 7 -> Direct3D 9
HandleExceptions    = 1      ; catches/recovers from common crashes in old games
FixPerfCounterUptime = 1     ; fixes timer/uptime-overflow issues some old games hit

[d3d9]
FullscreenWindowMode = 0     ; off by default — see below
```

> **If you get a crash when switching to fullscreen,** open `dxwrapper.ini` and set `FullscreenWindowMode = 1` under `[d3d9]`. This switches Fusion EX to borderless fullscreen instead of true exclusive fullscreen, which avoids the exact mode-switch that can crash on some systems (it's not universal, so it's left off by default rather than forced on everyone). If you turn this on, **leave Windows' own "Disable fullscreen optimizations" compatibility checkbox unchecked** — the two conflict and can cause their own crash.

Everything else is left at its default (`0`) and logging is disabled (`DisableLogging = 1`). Full option reference: <https://github.com/elishacloud/dxwrapper/wiki/Configuration>

### 2. Left stick + D-pad together — Xidi

Xidi presents XInput controllers (Xbox 360 / One / Series and compatible pads) to old DirectInput games as regular joysticks. With the `DigitalGamepad` mapper, the D-pad is mapped onto the **X and Y axes** — the same axes as the left stick — so both work at the same time.

| File | Role |
| --- | --- |
| `dinput.dll` | Xidi — Windows loads this instead of the system `dinput.dll` |
| `xidi.ini` | Configuration |

Version: **Xidi 4.3.1**

```ini
;maps the D-pad onto the left stick's X and Y axes, so the D-pad
;and the analog stick both work as input at the same time
[Mapper]
Type = DigitalGamepad

;some controllers report fewer buttons than expected; without this,
;Xidi/Fusion can stop scanning early and miss the rest
[Workarounds]
IgnoreEnumObjectsCallbackReturnCode = yes

[Log]
Enabled = no
Level = 4

;maps the stick's circular range to a square one, so digital diagonals
;(from the DigitalGamepad mapper above) register at full strength instead
;of falling short because the stick ran out of travel on the diagonal
[Properties]
CircleToSquarePercentStickLeft      = 100

;small buffer around center so stick drift on a worn/cheap controller
;doesn't register as a phantom diagonal press
DeadzonePercentStickLeft            = 10
```

Full option reference: <https://github.com/samuelgr/Xidi/wiki/Configuration>

### 3. Modified `Fusion EX.exe`

`Fusion EX.exe` is a modified build of the original 3.64 executable:

- **Rebranded as Fusion EX** — window title, About box, menus, and dialog/message text throughout now read "Fusion EX".
- **New icon** and a proper file-properties entry (right-click → Properties → Details shows Fusion EX, version, and credits).
- **"None" added to the Render Plugin menu**, so you can turn off plugin filtering without switching render mode separately.
- **Reorganized menus** — options are regrouped into *Emulation*, *Audio*, *Tools* and *Options* menus, with a *Recent Files* list, and the video menu has tidy *Scanlines*, *TV Mode* and *Aspect Ratio* submenus.
- **DPI-aware** and using the modern (v6) Windows common controls, so the UI isn't blurry-scaled on high-DPI displays.

The original, untouched executable is kept as **`Fusion.exe`** — `Fusion EX.exe` is the modified build, named differently so the two are never mixed up.

---

## Requirements

- Windows (32-bit or 64-bit) with Direct3D 9 support
- *(Optional)* An **XInput** gamepad — Xbox 360 / One / Series controllers, or any pad presented to Windows as XInput (for example via Steam Input or DS4Windows)

---

## Folder contents

| Path | Description |
| --- | --- |
| `Fusion EX.exe` | Kega Fusion 3.64, modified and rebranded as Fusion EX — see above |
| `Fusion.exe` | Original, unmodified Kega Fusion 3.64 |
| `ddraw.dll`, `dxwrapper.dll`, `dxwrapper.ini` | DxWrapper (60 FPS / full-screen fix) |
| `dinput.dll`, `xidi.ini` | Xidi (gamepad mapping) |
| `BIOS/` | BIOS images used for Sega CD, 32X, Master System, Game Gear, etc. |
| `Plugins/` | Video render plugins (`.rpi`): 2xSaI, hq2x/3x/4x, Scale2x/3x/4x, xBRZ, MDNTSC, … |
| `Patch/` | Cheat / patch files (`.pat`) for Mega Drive (Genesis), Master System and Game Gear |
| `Video Codec/` | Kega Game Video codec for AVI recording |
| `Readme.txt`, `History.txt` | Original Kega Fusion documentation and changelog |

### Using the cheat patches

A patch file must have exactly the same name as the ROM it belongs to — e.g. the ROM `Altered Beast (USA).zip` uses `Altered Beast (USA).pat` — and live in the `Patch` folder. Rename a `.pat` file if your ROM is named differently. See `Patch/Kega Fusion Cheat Code Pack.txt` for details.

---

## Troubleshooting

**Controller isn't detected / buttons do nothing**
Xidi only works with **XInput** controllers. Connect the pad first, set the controller port to *Joystick*, and re-bind with **DEFINE**. To diagnose, set `Enabled = yes` under `[Log]` in `xidi.ini`; Xidi will write a log file to your desktop.

**D-pad works but the stick doesn't (or vice versa)**
Make sure `xidi.ini` still has `Type = DigitalGamepad` under `[Mapper]`.

**Crash when switching to fullscreen**
Set `FullscreenWindowMode = 1` under `[d3d9]` in `dxwrapper.ini` (see above). Don't combine this with Windows' own "Disable fullscreen optimizations" compatibility checkbox — the two can conflict.

**Black screen, crash, or graphics glitch after launching**
Try updating your graphics drivers, then temporarily disable DxWrapper by renaming `ddraw.dll` to `ddraw.dll.off` — Fusion will fall back to the built-in DirectDraw. If that fixes it, the problem is in the DirectX translation layer rather than the emulator.

**Wrappers don't seem to be active**
Confirm `ddraw.dll`, `dxwrapper.dll`, `dxwrapper.ini`, `dinput.dll` and `xidi.ini` sit **next to `Fusion EX.exe`**, not in a subfolder.

### Going back to stock behaviour

- **Without DxWrapper:** remove (or rename) `ddraw.dll`, `dxwrapper.dll` and `dxwrapper.ini`.
- **Without Xidi:** remove (or rename) `dinput.dll` and `xidi.ini`.

Both `Fusion EX.exe` and `Fusion.exe` load whichever wrapper DLLs are in the folder, so remove them to get fully stock behaviour.

---

## Credits

- **Kega Fusion** — © Steve Snake, 2010. All emulator code is his work.
- **DxWrapper** — Elisha Riedlinger — <https://github.com/elishacloud/dxwrapper>
- **Xidi** — Samuel Grossman — <https://github.com/samuelgr/Xidi>
- **Fusion EX repackaging** — Hazem Abdelghani — <https://github.com/hazem-abdelghani/Kega-Fusion-EX>

This is an unofficial repackage and is not affiliated with or endorsed by any of the authors above. Please refer to each project's own repository or documentation for its license terms. No games are included — you must supply your own legally obtained ROMs and disc images.
