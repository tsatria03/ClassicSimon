---
name: project_score_tracking
description: "Best single-game score per pack per difficulty, saved to scores.csg; View scores menu in each pack's menu reads the three difficulty lines; autopilot runs never count."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16. ClassicSimon tracks a **high score = the best single game** (longest sequence memorized in one playthrough), **per pack, per difficulty** — NOT a cumulative total across games, and NOT session-only. It persists to `scores.csg` (see [[project_save_data_layout]]).

**Storage & functions** (`main/functions/scorefuncts.nvgt`, using the `sc` savedata instance):
- Key = `best_key(pack, dif)` → `"best_" + pack + "_" + string(dif)` (`dif` is `difmod`, 1/2/3).
- `record_score(pack, dif, score)` — `sc.load()` fresh (so other packs' records aren't wiped), and if `score` beats the stored best (or none exists) it ensures `savePath` exists, `sc.add`/`sc.save`. **Returns true ONLY when it beat an existing record** — so the first-ever game sets the record silently.
- `score_line(label, pack, dif)` — `"Easy. No score recorded."` when the key is absent, else `"Easy. Best score, N sequences."` (with `1 sequence` handled). Assumes `sc` already loaded.

**Recording point:** `game_over()` in `main/globals/game.nvgt`. After computing `score = sequence.length() - 1`, it calls `record_score(simontype, difmod, score)` **only when `!autopilot`**, and appends " New best!" to the spoken game-over line when that returns true. **Autopilot never records** (the computer is playing, not the player).

**Viewing:** `viewscores()` in `main/menus/menu.nvgt`, reached from `pack_options_menu` (Start game / Learn tones / **View scores** / Back). Read-only menu: `sc.load()` once, then three `score_line` items (Easy/Medium/Hard) + Back, using the `focus = res - 1` fix ([[project_custom_menu_run_indexing]]) so Enter on a line doesn't jump. Escape/Back returns silently (no cancel speech — see [[feedback_menus_say_canceled]]). Player-facing docs (changelog/readme) for this were deferred by the dev to a later pass.
