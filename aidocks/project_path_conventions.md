---
name: project_path_conventions
description: "The src/ (code) + csm/ (assets+launcher) + build/ + releases/ split, the cwd=csm/ trick, FLAT glob includes, and the #pragma asset caveat."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

ClassicSimon separates code from runtime assets into top-level folders (like the sibling games) — but it does **NOT** have the `src/includes/main/<subdir>/` reorg; its includes are **flat** (see [[project_include_tree]]).

- **`src/`** — code only. Entry `src/csm.nvgt`, plus a flat `src/includes/` (glob-included).
- **`csm/`** — runtime assets + the launcher: `csm/csm.py`, `csm/docks/`, `csm/lib/` (runtime DLLs + 7zr/lame), `csm/sounds/` (`misc/` + the Simon packs under `sounds/simons/<pack>/`). No `data/` folder.
- **`build/`** — the build/release pipeline (`tools.py`, `tools.ini`, `version.txt`) — see [[project_build_pipeline]].
- **`releases/`** — compiled output + archives (gitignored).

**The cwd trick (the key mechanism):** `csm/csm.py` runs `../src/csm.nvgt` through `C:\nvgt\nvgt.exe` but sets **cwd = `csm/`**. So:
- `#include"includes/*.nvgt"` resolves relative to the **script** → `src/includes/`.
- bare `sounds/...`, `docks/...`, `lib/...` strings resolve relative to **cwd** → `csm/`. This is why `dlgplay("sounds/misc/logo.ogg")`, `find_directories("sounds/simons/*")`, `find_sound("sounds/simons/"+pack+"/...")`, and the menu sounds all resolve under `csm/sounds/`.

All in-code sound paths are already cwd-relative — they just need the launcher's cwd; there is no per-file "sounds/" repointing to do.

**`#pragma asset` caveat:** `csm.nvgt` still has `#pragma asset sounds` and `#pragma document docks` at the top. Those are **compile-time** directives that look for the asset *relative to the script* (`src/`), where the assets do NOT live (they're in `csm/`). At **runtime** (interpreted via `csm.py`) they're ignored, so running is fine. For a `nvgt -c` compile, `build/tools.py` copies the assets into the bundle itself — so if the pragma trips the compiler on the missing `src/sounds`, the fix is to drop those two pragma lines (the copy step covers bundling). See [[project_build_pipeline]].
