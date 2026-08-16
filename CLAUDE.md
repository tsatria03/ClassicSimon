# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository. **It is a lean dispatcher:** it orients you to what the project is and its shape, then points to focused memory files (`[[name]]`) for the deep detail. When you start work in an area, read its linked memory first.

**Memory location:** all memory files (`[[name]]` links and the `MEMORY.md` index) live in the repo's **`aidocks/`** directory (`aidocks/<name>.md`). Read memory from there, and write any new or updated memory there — not the `~/.claude` memory store. `aidocks/MEMORY.md` is the index; add a one-line pointer there for every new memory. Keep this file under 40,000 chars — move detail into memory ([[feedback_claudemd_length]]).

## What this is

ClassicSimon is an audio-only (blind-accessible) **Simon memory game** written in **NVGT** (Non-Visual Game Toolkit, an AngelScript-based engine). All code is `.nvgt`. The game plays a growing sequence of four tones and the player repeats it with the arrow keys, one tone longer each round until they miss. Pick a sound "pack" (instrument) and a difficulty, or use the Learn-tones browser.

**It's a collaboration/mirror:** the original game is by the user's friend **Keri-marie Kelly**, who gave permission to extend it — so it isn't originally the user's (hence releases carry no password). Full picture and the load-bearing systems: **[[project_game_vision]]**. The pack system: **[[project_simon_pack_system]]**.

## Layout — code and assets are split, and includes now use the sibling nested `main/` tree

- **`src/`** — code only. Slim entry `src/csm.nvgt` (just `#include"includes/includes.nvgt"` + `main()`), plus the **nested** `src/includes/main/{deps,functions,globals,menus}/` tree (reorged from flat on 2026-08-16).
- **`csm/`** — runtime assets + launcher: `csm/csm.py`, `csm/docks/`, `csm/lib/` (runtime DLLs + 7zr/lame), `csm/sounds/` (`misc/` + the Simon packs under `sounds/simons/<pack>/`). No `data/` folder.
- **`build/`** — build/release pipeline (`tools.py` via `tools.bat`; `tools.ini`; `version.txt`). **`releases/`** — compiled output (gitignored).
- **`aidocks/`** — this project's memory folder (committed). "Memory" always means this folder.

**The cwd trick:** `csm/csm.py` runs `../src/csm.nvgt` through NVGT but sets **cwd = `csm/`**, so bare `sounds/…`, `docks/…`, `lib/…` strings resolve under `csm/`, while `#include"includes/includes.nvgt"` (the manifest, which then globs `main/{deps,functions,globals,menus}/*`) resolves against the script → `src/includes/`. Full path map + the (now-removed) `#pragma asset` caveat: **[[project_path_conventions]]**. Include tree + manifest: **[[project_include_tree]]**.

**Engine is pinned to the legacy fork at `C:\nvgt`** (BASS audio); the miniaudio build would be `C:\nvgt2` (not installed) — don't target it or suggest upgrading. **[[project_engine_pinned_nvgt2]]**.

**Line endings: this repo is LF** (`.gitattributes` `eol=lf`), UNLIKE the CRLF-enforced sibling games — create new files as LF. **[[project_repo_hygiene]]**.

## Running & building

No test suite or linter. The game launches via `csm/csm.py` (runs `src/csm.nvgt` under `C:\nvgt\nvgt.exe`, cwd `csm/`, compile errors → `csm/errors.txt`) and is compiled/packaged/released via `build/tools.py` — but **the dev runs and builds, not Claude** ([[feedback_dont_run_or_build_the_game]]). The build tools are SimpleFighter's pipeline trimmed: **no installer, no website step, no archive password**. Version source of truth is `build/version.txt`, mirrored into `src/includes/version.nvgt` (pulled first by the `includes.nvgt` manifest) on launch and compile — never hand-edit `version.nvgt`, and don't re-declare `string version` (or the `main/globals/dec.nvgt` globals) anywhere (duplicate global). Full pipeline: **[[project_build_pipeline]]**.

## Where the detail lives (read before working in an area)

- **Audio** (four tones + fail as direct `sound` objects; menu pool) → **[[project_audio_model]]**; the **sound packs** → **[[project_simon_pack_system]]**.
- **Repo hygiene** (LF `.gitattributes`, gitignore) → **[[project_repo_hygiene]]**.
- **Player-facing docs** — `csm/docks/` (`changelog.txt`, `credits.txt`, `readme.txt`). Rules: **[[feedback_changelog_rules]]**, **[[feedback_dock_line_length_1024]]**.

## Compile-breakers — read before writing .nvgt (the game runs from source, so a compile error = won't launch)

- **[[project_angelscript_braceless_if]]** — a braceless branch holds only one statement; a second orphans the `else`.
- **[[project_angelscript_reserved_words]]** — don't name a variable `out` (or in/inout/shared/final/from…).
- **[[project_nvgt_key_pressed_oneshot]]** — read a multi-purpose key once and branch inside, never two sibling ifs on the same key.
- **[[project_nvgt_sound_preload_cache]]** — a reused filename replays the old cached clip; use a fresh name or `allow_preloads=false`.

## Working style (follow these)

- **[[feedback_confirm_before_implementing]]** — a design discussion or a `?` is a request for a plan, not a green light. Wait for explicit go-ahead.
- **[[feedback_ask_one_question_at_a_time]]** — one question per turn, then wait.
- **[[feedback_list_modified_files]]** — end every editing turn with a bare-filename "Files changed:" list, then a relaunch note.
- **[[feedback_verify_code_while_fixing]]** — re-locate by symbol, confirm the finding is true, flag adjacent bugs.
- **[[feedback_check_git_log_for_commits]]** / **[[feedback_stage_commits_before_big_changes]]** — the dev commits their own work; check state, don't commit unless asked, flag break points before risky stages.
- **[[feedback_one_sentence_game_messages]]**, **[[feedback_menus_say_canceled]]**, **[[feedback_yes_no_menu_labels]]** — in-game/UI conventions.
- **[[feedback_multiline_comment_style]]**, **[[feedback_dont_flag_indentation]]**, **[[feedback_no_crlf_normalization]]** — smaller standing rules.

Repo-root `README.md` is a one-line stub; the real readme is `csm/docks/readme.txt`. `CLAUDE.md` and `aidocks/` are committed (not gitignored).
