---
name: project_build_pipeline
description: "csm.py launcher + build/tools.py (SimpleFighter-based, NO installer / NO website / NO password); version mirroring; ASSET_FOLDERS=docks,lib,sounds."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

No test suite or linter. Running and building are scripted but manual; **the dev runs them, not Claude** ([[feedback_dont_run_or_build_the_game]]).

**Launcher — `csm/csm.py`:** runs `../src/csm.nvgt` under `C:\nvgt\nvgt.exe` with cwd = `csm/` (the [[project_path_conventions]] cwd trick), `CREATE_NO_WINDOW`, hides its own console, and watches ~5s for an early compile-error exit → writes `csm/errors.txt` (+ a popup) on failure, otherwise detaches. Absent/empty `errors.txt` = no errors. It mirrors `build/version.txt` → `src/includes/version.nvgt` before launch (LF).

**Release — `build/tools.py`** (via `build/tools.bat`, or 4 flag args non-interactively). It is SimpleFighter's pipeline **trimmed for this game — NO Inno installer, NO website step, NO archive password**. Interactive menu = commit tools (commit / undo / push / history / create-tag) + release stages:
- **compile:** mirror version into `version.nvgt`, run `nvgt -c -Q csm.nvgt` from `src/` (bundle lands in `src/csm`), copy `docks`,`lib`,`sounds` (`ASSET_FOLDERS`) from `csm/` into the bundle, move it to `releases/windows/ClassicSimon_windows_portable/csm`.
- **package:** a **plain (unencrypted) 7z portable archive** `ClassicSimon_windows_portable.7z` — no `-p`/`-mhe` (ClassicSimon wasn't originally the user's game; released openly, no password).
- **release:** `gh release create`, attaching the archive.

**`csm/lib`** holds the runtime DLLs (BASS/bass_fx/bassmix, Tolk/SAAPI64/nvdaControllerClient64/zdsrapi, phonon, nvgt_curl) plus `7zr.exe`/`lame.exe`; it's gitignored but present locally and MUST be in `ASSET_FOLDERS` so compiled builds carry it. **Config:** `build/tools.ini` `[game]` name + nvgt_file only (no password/site/installer); shared `nvgt`/`sevenzip`/`gh` paths in `~/.game_tools/tools.ini`. Version source of truth is `build/version.txt` ([[feedback_update_build_version_txt]]). If a `nvgt -c` compile ever fails on `#pragma asset sounds` (looks for `src/sounds`), drop that pragma — tools.py already copies the assets ([[project_path_conventions]]).
