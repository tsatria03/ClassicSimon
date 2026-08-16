---
name: project_include_tree
description: "FLAT src/includes/ glob-included via #include\"includes/*.nvgt\" (no main/ subtree); version.nvgt provides the version; rotation.nvgt backs sound_pool."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

Entry `src/csm.nvgt` pulls in everything with a single **flat wildcard glob**: `#include"includes/*.nvgt"`. There is **no `main/` subtree** and no per-category folders — every include sits directly in `src/includes/`. (This is why ClassicSimon needed no reorg, unlike the sibling games.) A new `.nvgt` dropped into `src/includes/` is auto-included.

Current `src/includes/` files:
- **Helpers/stdlib (vendored):** `bgt_compat`, `instance`, `sound_pool`, `rotation`, `form`, `speech`, `custom_menu`, `dlgplayer`.
- **Game code:** `extrafuncts` (holds `find_sound`, `load_simon_pack`, tone play/stop, `learnsounds`, difficulty), `game` (the round/sequence loop), `menu` (menus + pack picker), `setupmenu` (menu-sound config).
- **`version.nvgt`** — `string version = "1.0";`, the glob-included version global (mirror of `build/version.txt`; see [[project_build_pipeline]]). Do NOT also declare `string version` in `csm.nvgt` — that would be a duplicate-global compile error.

**Vendoring note:** `sound_pool.nvgt` does `#include "rotation.nvgt"`, and `rotation.nvgt` (defines `pi`/`calculate_theta`) is present in `src/includes/` — so the glob covers it. Keep any vendored helper's own `#include` deps present in `src/includes/`. Engine: [[project_engine_pinned_nvgt2]].
