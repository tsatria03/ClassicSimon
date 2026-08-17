---
name: project_game_modes
description: "Four game modes (gamemode 1-4) form a 2x2 of carryover (grow/fresh) x direction (forward/backward): Classic, Classic Reverse, Shuffle, Shuffle Reverse. Chosen in modemenu before diffmenu."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16. The `int gamemode` global (`dec.nvgt`, default 1) selects one of four modes, which form a clean 2×2 — **carryover** (grow the sequence vs. fresh random each round) × **direction** (echo forward vs. backward):

| gamemode | Mode | Carryover | Direction |
|---|---|---|---|
| 1 | Classic | grows (append) | forward |
| 2 | Classic Reverse | grows (append) | backward |
| 3 | Shuffle | fresh each round | forward |
| 4 | Shuffle Reverse | fresh each round | backward |

**Two independent switches implement all four** (they compose):
- `next_round()` (`gamefuncts.nvgt`): `if(gamemode == 3 || gamemode == 4)` regenerate the whole sequence to length+1; else append one random tone.
- `game_loop()` (`game.nvgt`): `expected_index = (gamemode == 2 || gamemode == 4) ? length-1-player_index : player_index` — the Reverse variants compare input back-to-front.

Reverse is **order** reversal, NOT direction/arrow mirroring — key↔tone is a FIXED pairing (left=1, down=2, up=3, right=4) that never changes in any mode; only the order (and whether the sequence regenerates) differs. In round 1 a single tone can't be reordered, so Classic and Classic Reverse look identical there — that is correct, not a bug.

**Menus/naming:** `modemenu()` in `menu.nvgt` runs BEFORE `diffmenu()` (the difficulty menu — note `diffmenu` is the renamed old `modemenu`; the pack "start" handler calls `modemenu()` which then calls `diffmenu()`). Item ids: `classic` / `classicreverse` / `shuffle` / `shufflereverse`; each menu label carries a one-sentence description appended after the name ("Classic. Repeat the same sequence in order…"). `get_mode_name(int)` returns readable lowercase names ("classic", "classic reverse", "shuffle", "shuffle reverse"). Autopilot is mode-agnostic (it just demos playback + random-fails).

Scores are kept per pack **per mode** per difficulty — see [[project_score_tracking]].
