---
name: project_score_tracking
description: "Best single-game score per pack per MODE per difficulty, saved to scores.csg; View scores is a mode-picker drill-down; autopilot runs never count."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16. ClassicSimon tracks a **high score = the best single game** (longest sequence memorized in one playthrough), **per pack, per mode, per difficulty** — NOT a cumulative total across games, and NOT session-only. It persists to `scores.csg` (see [[project_save_data_layout]]). The **mode** dimension was added when the four game modes landed ([[project_game_modes]]).

**Storage & functions** (`main/functions/scorefuncts.nvgt`, using the `sc` savedata instance):
- Key = `best_key(pack, mode, dif)` → `"best_" + pack + "_" + string(mode) + "_" + string(dif)` (`mode` is `gamemode` 1-4, `dif` is `difmod` 1-4: easy/medium/hard/insane).
- `record_score(pack, mode, dif, score)` — `sc.load()` fresh (so other records aren't wiped), and if `score` beats the stored best (or none exists) it ensures `savePath` exists, `sc.add`/`sc.save`. **Returns true ONLY when it beat an existing record** — so the first-ever game sets the record silently.
- `score_line(label, pack, mode, dif)` — `"Easy. No score recorded."` when the key is absent, else `"Easy. Best score, N sequences."` (with `1 sequence` handled). Assumes `sc` already loaded.

**Recording point:** `game_over()` in `main/globals/game.nvgt`. After computing `score = sequence.length() - 1`, it calls `record_score(simontype, gamemode, difmod, score)` **only when `!autopilot`**, and appends " New best score!" to the spoken game-over line when that returns true. **Autopilot never records** (the computer is playing, not the player).

**Viewing (drill-down):** `viewscores()` in `main/menus/menu.nvgt` (reached from `pack_options_menu`: Start game / Learn tones / **View scores** / Back) is the **mode picker** — intro "packname scores menu", items Classic / Classic reverse / Shuffle / Shuffle reverse / Back. Selecting one calls `viewscores_mode(mode)`, which `sc.load()`s and shows that mode's difficulty `score_line` items (Easy/Medium/Hard/Insane) + Back, intro "packname <mode name> scores". Both levels use the `focus = res - 1` fix ([[project_custom_menu_run_indexing]]) so Enter on a line doesn't jump, and Back/escape returns silently ([[feedback_menus_say_canceled]]).
