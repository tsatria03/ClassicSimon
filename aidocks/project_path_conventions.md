---
name: project_path_conventions
description: "The src/ (code) + csm/ (assets+launcher) + build/ + releases/ split, the cwd=csm/ trick, the nested main/ includes via the includes.nvgt manifest, and the (now-removed) #pragma asset caveat."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

ClassicSimon separates code from runtime assets into top-level folders (like the sibling games). As of the 2026-08-16 reorg it also adopted the sibling `src/includes/main/<subdir>/` nested layout, pulled by the `includes/includes.nvgt` manifest (see [[project_include_tree]]).

- **`src/`** — code only. Slim entry `src/csm.nvgt` (`#include"includes/includes.nvgt"` + `main()`), plus the nested `src/includes/main/{deps,functions,globals,menus}/` tree.
- **`csm/`** — runtime assets + the launcher: `csm/csm.py`, `csm/docks/`, `csm/lib/` (runtime DLLs + 7zr/lame), `csm/sounds/` (`misc/` + the Simon packs under `sounds/simons/<pack>/`). No `data/` folder.
- **`build/`** — the build/release pipeline (`tools.py`, `tools.ini`, `version.txt`) — see [[project_build_pipeline]].
- **`releases/`** — compiled output + archives (gitignored).

**The cwd trick (the key mechanism):** `csm/csm.py` runs `../src/csm.nvgt` through `C:\nvgt\nvgt.exe` but sets **cwd = `csm/`**. So:
- `#include"includes/includes.nvgt"` (and the manifest's nested `#include"main/…"` globs) resolve relative to the **script** → `src/includes/`.
- bare `sounds/...`, `docks/...`, `lib/...` strings resolve relative to **cwd** → `csm/`. This is why `dlgplay("sounds/misc/logo.ogg")`, `find_directories("sounds/simons/*")`, `find_sound("sounds/simons/"+pack+"/...")`, and the menu sounds all resolve under `csm/sounds/`.

All in-code sound paths are already cwd-relative — they just need the launcher's cwd; there is no per-file "sounds/" repointing to do.

**`#pragma asset` caveat (now resolved):** `csm.nvgt` used to carry `#pragma asset sounds` and `#pragma document docks` at the top. Those are **compile-time** directives that look for the asset *relative to the script* (`src/`), where the assets do NOT live (they're in `csm/`). The 2026-08-16 reorg **removed both pragma lines** — so this is no longer a live trip hazard. Bundling is unaffected: `build/tools.py` copies the assets (docks, lib, sounds) into the `nvgt -c` bundle itself. If you ever re-add such a pragma, expect it to fail on the missing `src/sounds`; don't. See [[project_build_pipeline]].
