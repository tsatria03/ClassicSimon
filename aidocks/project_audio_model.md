---
name: project_audio_model
description: "Four tones + a fail sound play as direct `sound` objects (not a spatial pool); menus use custom_menu's own pool. All clips are cwd-relative to csm/."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

ClassicSimon is a Simon (memory) game — audio is not spatial. The core sounds are **four tones + a fail sound**, held as plain `sound` objects in globals (`sound[] tone_sounds(4)`, `sound fail_sound` in `csm.nvgt`) and played directly (`tone_sounds[i-1].play()`, `fail_sound.play()`). The tones are loaded per pack by `load_simon_pack()` via `find_sound()` — see [[project_simon_pack_system]].

- **Menu audio:** the `custom_menu` class owns its own `sound_pool menupool` and background music; menu click/open/close/edge/enter/wrap sounds + `add_music("sounds/misc/menumusic.ogg")` are wired in `setupmenu.nvgt`.
- **Logo/dialog:** `dlgplayer.nvgt` (`dlgplay`) plays one-shots like `sounds/misc/logo.ogg`.
- `sound_pool`/`rotation` are vendored ([[project_include_tree]]) but the game doesn't do HRTF positioning of tones.

All clips are **cwd-relative** to `csm/` ([[project_path_conventions]]). Watch the preload cache if a clip's bytes change under a reused filename — [[project_nvgt_sound_preload_cache]]. When a new sound is needed, wire the playback to the intended filename now; the dev adds the `.ogg` later (don't create dummy files).
