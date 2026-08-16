---
name: project_include_tree
description: "NESTED src/includes/main/{deps,functions,globals,menus}/ pulled by the includes/includes.nvgt manifest; csm.nvgt is a slim entry; version.nvgt provides the version; rotation backs sound_pool."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

**As of the 2026-08-16 reorg, includes are NESTED, not flat.** Entry `src/csm.nvgt` is now a slim file — just `#include"includes/includes.nvgt"` plus `void main()`. It no longer holds the globals or the `#pragma asset`/`#pragma document` lines (both removed).

**The manifest:** `src/includes/includes.nvgt` is the include hub. It does, in order:
- `#include"version.nvgt"` (the version global),
- `#include"main/deps/*"`, `#include"main/functions/*"`, `#include"main/globals/*"`, `#include"main/menus/*"` (per-folder wildcard globs),
- then declares `typedef uint8 usmall; typedef int8 small;`.

A new `.nvgt` file is auto-included **only if** it lands in one of those four `main/<subdir>/` folders (or is added to the manifest). Dropping a file loose in `src/includes/` no longer picks it up — the top-level is no longer globbed.

**Current tree under `src/includes/`:**
- `includes.nvgt` — the manifest (above). `version.nvgt` — `string version = "1.0";`, the version global (mirror of `build/version.txt`; see [[project_build_pipeline]]). Both sit directly in `includes/`.
- `main/deps/` — vendored stdlib/helpers: `bgt_compat`, `instance`, `sound_pool`, `rotation`, `form`, `speech`, `custom_menu`, `dlgplayer`.
- `main/functions/` — `extrafuncts` (generic helpers: `alt/control/shift_is_down`, `find_sound`, `sort_naturally`) and `gamefuncts` (game logic: `get_pressed_tone`, `load_simon_pack`, tone play/stop, `apply_difficulty`, `play_sequence`, `next_round`, `learnsounds`).
- `main/globals/` — `dec.nvgt` (all global variable declarations that used to be in `csm.nvgt`: `sequence`, `difmod`, `tone_sounds`, `fail_sound`, `m`, `gamstance`, `input_timer`, `simspeech`, etc.) and `game.nvgt` (the round/sequence loop: `game_loop`, `game_over`, `start_game`, `play_game`).
- `main/menus/` — `menu` (menus + pack picker) and `setupmenu` (menu-sound config).

Do NOT re-declare `string version` anywhere, and do NOT re-declare the `dec.nvgt` globals in `csm.nvgt` — either is a duplicate-global compile error.

**Do NOT flag `rotation.nvgt` as dead/unused.** It looks like an unrelated rotation/3D-distance library dropped in by the deps glob, but it is load-bearing: `sound_pool.nvgt:611` calls `calculate_theta(rotation)` for pan math, and `calculate_theta` lives only in `rotation.nvgt`. Removing the file (or its include) breaks the sound pool. A past session wrongly called it "~200 lines of dead weight" — it isn't. **Vendoring rule:** keep every vendored helper's own `#include` deps present under `main/deps/`. Engine: [[project_engine_pinned_nvgt2]].
