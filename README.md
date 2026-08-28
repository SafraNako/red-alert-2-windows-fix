# Red Alert 2 – Windows 10/11 Fix Guide

Command & Conquer: Red Alert 2 is a 2000-era game built on DirectDraw. Running it
on a modern Windows 10/11 machine with a modern GPU driver commonly breaks in a
few very specific, very fixable ways. This guide walks through the real root
causes (not just "try compatibility mode") in the order you're likely to hit
them.

## 1. Game window never appears / process runs but shows nothing

**Cause:** `RA2.INI` has a `ScreenWidth`/`ScreenHeight` that doesn't match a
resolution your monitor actually supports. The game tries to switch the display
to that mode, fails silently, and never creates a visible window.

**Fix:** Open `RA2.INI` in the game folder and set `ScreenWidth`/`ScreenHeight`
under `[Video]` to your monitor's actual native resolution (e.g. `1920`/`1080`).

```ini
[Video]
ScreenWidth=1920
ScreenHeight=1080
```

## 2. Game opens but shows a solid black screen

**Cause:** RA2 renders through DirectDraw in exclusive-fullscreen mode. Modern
GPU drivers (and Windows' own DWM fullscreen-optimization layer) frequently
can't initialize that mode correctly, so the window exists but nothing is
drawn to it.

**Fix, in order of how much they solve on their own:**

1. Right-click the game's `.exe` → Properties → Compatibility → check
   **"Disable fullscreen optimizations"**. Sometimes enough on its own.
2. If that's not enough, use **[cnc-ddraw](https://github.com/FunkyFr3sh/cnc-ddraw)**
   — an open-source `ddraw.dll` wrapper built specifically for classic
   Command & Conquer / DirectDraw-era games. It translates the old DirectDraw
   calls to a modern rendering path.
   - Download `cnc-ddraw.zip` from the [releases page](https://github.com/FunkyFr3sh/cnc-ddraw/releases)
   - Extract `ddraw.dll` and `ddraw.ini` directly into your RA2 install folder
     (next to the game's `.exe`)
   - Launch the game normally

This combination (resolution fix + cnc-ddraw) resolves the two most common
"RA2 won't display anything" reports on Windows 10/11.

## 3. Verify your install before chasing display bugs

A surprising number of "black screen" or "crashes on launch" reports turn out
to be an **incomplete install** — a missing companion DLL, a corrupted `.mix`
file, etc. — rather than a display bug. Before spending time on driver/DirectDraw
fixes:

- Confirm the game actually launches to *some* window state (check Task
  Manager: is the process alive? Does it have a window handle at all?) before
  assuming it's a rendering issue vs. a missing-file crash.
- If you're getting a `Windows can't find [file].dll` error dialog specifically
  (not a black screen), that's a missing-file problem, not a display problem —
  reinstalling or re-verifying your copy of the game is the fix, not a ddraw
  wrapper.

## 4. Want online multiplayer instead of just fixing the original files?

Rather than trying to patch 2000s-era networking code back to life, use
**[CnCNet](https://cncnet.org)** — the actively maintained community project
for playing classic C&C games online today. It also happens to iron out most
of the display/compatibility issues above as part of its own launcher.

## 5. Trainers / Cheat Engine for offline skirmish play

If you want cheats for single-player/skirmish (not online play):

- Pre-built trainers (e.g. from [WeMod](https://www.wemod.com)) are convenient,
  but they target exact memory offsets from one specific build of the game
  (usually the current Steam build). If your copy's executable isn't
  byte-identical to that build, the trainer can attach and show its overlay
  without the actual memory patches taking effect.
- **[Cheat Engine](https://www.cheatengine.org)** works by scanning the
  process's live memory for the value you're looking for (e.g. your current
  credits), rather than relying on a fixed offset. That makes it slower to set
  up but immune to this build-mismatch problem — it'll work against any build
  of the game.

## Credits

- [cnc-ddraw](https://github.com/FunkyFr3sh/cnc-ddraw) by FunkyFr3sh (fork of mvdhout1992/cnc-ddraw)
- [Cheat Engine](https://github.com/cheat-engine/cheat-engine) by Dark Byte
- [CnCNet](https://cncnet.org)

---
*Written up after a long, real troubleshooting session — hope it saves someone
else the same afternoon.*
