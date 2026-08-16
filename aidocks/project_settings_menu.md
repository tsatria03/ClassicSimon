---
name: project_settings_menu
description: "The Settings menu (menu.nvgt settingsmenu) holds session-only toggles; 'Spoken hints' maps to the simspeech global; cross-launch persistence is intended but deferred."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16. `settingsmenu()` in `main/menus/menu.nvgt` is reachable from the main menu (Play / **Settings** / Exit). It holds stateful on/off toggle items plus Back, in this order:

- **"Play logo at startup"** — flips the `logoplay` global (`dec.nvgt`, default `true`). `csm.nvgt`'s `main()` wraps the logo speech + `dlgplay("sounds/misc/logo.ogg")` in `if(logoplay){...}` then calls `mainmenu()` once. NOTE: because the logo plays at startup *before* Settings is reachable, this toggle has NO effect on the current session — it only matters once settings persist across launches (see below).
- **"Spoken hints"** — flips the `simspeech` global (`dec.nvgt`, default `true`, see the default note below). Gates the "Round N" announcements, the "go" hand-off cue in `next_round()` (suppressed under autopilot via `simspeech && !autopilot`), and the "left/down/up/right/F" labels in `learnsounds()` — NOT menu speech or game messages, which are always on.

**Toggle mechanics:** each item's label shows its current state ("…, currently on/off"). The loop tracks `focus = res` (the selected index) and calls `m.run(focus, true, true)`, so after a toggle the menu re-opens focused on the item you just flipped and speaks its updated label — the player hears the new state from the menu itself. (A separate `speak()` confirmation would be cut off by the menu's own intro, since `speak()` interrupts.) Back/escape speaks "canceled" per [[feedback_menus_say_canceled]].

**Default: `simspeech` is `true`** (set in `dec.nvgt`) — spoken hints are ON by default and the toggle is opt-out, not opt-in. (Changed 2026-08-16; it originally defaulted to `false`.)

**Settings are session-only.** Every setting (this toggle, `difmod`, `autopilot`, etc.) is an in-memory global that resets to its default each launch — there is no save/load. **Cross-launch persistence is a known, intended-but-deferred feature** (the dev said it "can be arranged later"), not an oversight. If asked to add it, that means introducing a config file read/write, since none exists yet.
