---
name: project_settings_menu
description: "The Settings menu (menu.nvgt settingsmenu) holds session-only toggles; 'Spoken hints' maps to the simspeech global; cross-launch persistence is intended but deferred."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16. `settingsmenu()` in `main/menus/menu.nvgt` is reachable from the main menu (Play / **Settings** / Exit). It currently holds one toggle plus Back:

- **"Spoken hints"** — a stateful on/off item ("Spoken hints, currently on/off") that flips the `simspeech` global (in `main/globals/dec.nvgt`). It uses `m.run(0, true, true)` so the focused item is spoken on entry and after each toggle — the player hears the new state from the menu itself (a separate `speak()` confirmation would be cut off by the menu's own intro, since `speak()` interrupts). Back/escape speaks "canceled" per [[feedback_menus_say_canceled]]. `simspeech` gates the "Round N" announcements, the "go" hand-off cue in `next_round()`, and the "left/down/up/right/F" labels in `learnsounds()` — NOT menu speech or game messages, which are always on.

**Default: `simspeech` is `true`** (set in `dec.nvgt`) — spoken hints are ON by default and the toggle is opt-out, not opt-in. (Changed 2026-08-16; it originally defaulted to `false`.)

**Settings are session-only.** Every setting (this toggle, `difmod`, `autopilot`, etc.) is an in-memory global that resets to its default each launch — there is no save/load. **Cross-launch persistence is a known, intended-but-deferred feature** (the dev said it "can be arranged later"), not an oversight. If asked to add it, that means introducing a config file read/write, since none exists yet.
