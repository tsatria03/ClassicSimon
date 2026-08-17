---
name: project_menu_navigation
description: "Menus back out via RETURN-based unwinding, not mainmenu() calls. Escape from a game or learn tones goes to the pack's menu, using the quit_to_pack flag. mainmenu() is called ONCE, from startup."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

**Menu chain (each level calls the next):** `mainmenu()` → `packmenu()` → `pack_options_menu()` (the "pack's menu": Start game / Learn tones / View scores / Back) → `modemenu()` (game mode) → `diffmenu()` (difficulty + autopilot; this is the RENAMED old modemenu) → `play_game()` → `game_loop()` / `game_over()`.

**Backing out is RETURN-based — never call `mainmenu()` to navigate backward.** `mainmenu()` has its own infinite loop, so calling it from deeper in the chain stacks a *new* main menu on the call stack instead of unwinding (this was a real bug: escape in Learn tones and in a game both jumped to the main menu via `mainmenu()`). `mainmenu()` must be called exactly ONCE, from `csm.nvgt` `main()`.

**How escape-from-a-game returns to the pack's menu (fixed 2026-08-16):**
- `game_loop()` (escape mid-game) and `game_over()` (escape on the fail screen) set the global `bool quit_to_pack = true` (in `dec.nvgt`) and `return` — they do NOT call `mainmenu()`.
- `play_game()`, `diffmenu()`, and `modemenu()` each do `if(quit_to_pack) return;` right after their sub-call, so the flag unwinds the whole chain up to `pack_options_menu`.
- `pack_options_menu()` resets `quit_to_pack = false` at the top of its `while` loop, so the flag never leaks into later navigation.
- `learnsounds()` (escape) simply `return`s (it's called directly from `pack_options_menu`, so a plain return lands on the pack's menu).
- Enter on the fail screen leaves `quit_to_pack` false, so `play_game()`'s loop restarts the game (retry). Backing out of the difficulty menu (no game played) returns to the mode menu (flag false), not the pack menu.

Menu index-base gotcha when re-focusing: [[project_custom_menu_run_indexing]].
