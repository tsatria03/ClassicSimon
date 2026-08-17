---
name: feedback_dont_modify_deps
description: "Never modify vendored dep includes (src/includes/main/deps/*) — treat them as read-only libraries; work around their behavior in game code instead."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

**Do NOT modify any file under `src/includes/main/deps/`** (`custom_menu`, `speech`, `sound_pool`, `rotation`, `form`, `bgt_compat`, `instance`, `dlgplayer`, `savedata`, etc.). They are vendored third-party/shared libraries — treat them as read-only. Stated by the dev 2026-08-16.

**Why:** These are reused across the dev's games and maintained upstream; local edits would diverge from the shared copies and could be overwritten or cause inconsistencies between projects.

**How to apply:** When a dep's behavior needs changing, work around it in game code (the `main/functions`, `main/menus`, `main/globals` layers) rather than editing the dep. Example: the dev wanted no "canceled" speech ([[feedback_menus_say_canceled]]); the game-level menus were changed, but the dep-level cancel strings in `custom_menu.nvgt` ("Cancelled." on search cancel) and `speech.nvgt` ("Canceled" on input cancel) were deliberately LEFT ALONE. If a dep genuinely must change, ask the dev first. See the dep list in [[project_include_tree]].
