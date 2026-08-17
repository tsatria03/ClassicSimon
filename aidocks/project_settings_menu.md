---
name: project_settings_menu
description: "The Settings menu (menu.nvgt settingsmenu) holds on/off toggles (logoplay, simspeech) that now persist across launches via the savedata st instance and readpreffs/writepreffs."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16. `settingsmenu()` in `main/menus/menu.nvgt` is reachable from the main menu (Play / **Settings** / Exit). It holds stateful on/off toggle items plus Back, in this order:

- **"Play logo at startup"** — flips the `logoplay` global (`dec.nvgt`, default `true`). `csm.nvgt`'s `main()` calls `readpreffs()` first, then wraps the logo speech + `dlgplay("sounds/misc/logo.ogg")` in `if(logoplay){...}` and calls `mainmenu()` once. Since settings now persist (below) and load before the logo check, turning this off takes effect on the **next** launch (not the current one — the toggle only becomes reachable after the logo already played this run).
- **"Spoken hints"** — flips the `simspeech` global (`dec.nvgt`, default `true`, see the default note below). Gates the "Round N" announcements, the "go" hand-off cue in `next_round()` (suppressed under autopilot via `simspeech && !autopilot`), and the "left/down/up/right/F" labels in `learnsounds()` — NOT menu speech or game messages, which are always on.

**Toggle mechanics:** each item's label shows its current state ("…, currently on/off"). The loop tracks `focus = res` (the selected index) and calls `m.run(focus, true, true)`, so after a toggle the menu re-opens focused on the item you just flipped and speaks its updated label — the player hears the new state from the menu itself. (A separate `speak()` confirmation would be cut off by the menu's own intro, since `speak()` interrupts.) Back/escape returns silently — nothing says "canceled" ([[feedback_menus_say_canceled]]).

**Default: `simspeech` is `true`** (set in `dec.nvgt`) — spoken hints are ON by default and the toggle is opt-out, not opt-in. (Changed 2026-08-16; it originally defaulted to `false`.)

**Settings now persist across launches** (added 2026-08-16 via the ToyMania `savedata` pattern — see [[project_save_data_layout]]). `savedata st` (in `dec.nvgt`) points at `DIRECTORY_APPDATA + "tsatria03/ClassicSimon/saves/settings.csg"` (`.csg`, ClassicSimon's own extension — not ToyMania's `.tmg`). `readpreffs()`/`writepreffs()` live in `main/functions/savefuncts.nvgt`: `readpreffs()` (called at the top of `main()`'s else block) does `st.load()` then guarded `if(st.d.exists(key)) var = st.read_bool(key)` for `logoplay` and `simspeech`; `writepreffs()` ensures `savePath` exists (`directory_create`), `st.add`s both, and `st.save()`s. `settingsmenu()` calls `writepreffs()` after every toggle, so changes persist immediately. NOTE: the `st` encryption key is currently **blank** (`""`, plaintext) — the dev intends to fill it in; if you edit that line, keep it blank unless told otherwise. Only `logoplay` and `simspeech` are persisted so far; `difmod`/`autopilot` remain per-session game choices.
