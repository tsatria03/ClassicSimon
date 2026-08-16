---
name: project_simon_pack_system
description: "Selectable Simon sound packs under csm/sounds/simons/<pack>/ (1.ogg..4.ogg + fail.ogg); find_directories enumerates them, load_simon_pack loads via find_sound (random pick)."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

Each Simon "instrument" is a **sound pack**: a folder under `csm/sounds/simons/<pack>/` containing `1.ogg`, `2.ogg`, `3.ogg`, `4.ogg` (the four tones) and `fail.ogg`. Shipped packs include e.g. `carabiner`, `Little Pocket`, `Little trixter`.

- **Discovery:** `packmenu()` (in `menu.nvgt`) lists packs via `find_directories("sounds/simons/*")` — dropping a new pack folder in makes it selectable, no code change. The chosen pack name is stored in the global `simontype`.
- **Loading:** `load_simon_pack(pack)` (in `extrafuncts.nvgt`) loads `sounds/simons/<pack>/1..4.ogg` into `tone_sounds[]` and `fail.ogg` into `fail_sound`, guarding with "Missing tone N" / "Missing fail sound" speech; it early-returns if `loaded_pack` already equals `pack`.
- **`find_sound()`** resolves each path with `find_files()` and returns a **random** matching file (so a pack folder can hold multiple variants of a tone and one is picked). It returns `""` if nothing matches.

**How to apply:** reference pack clips through the `sounds/simons/<pack>/...` layout and `find_sound()`, keep the `1..4.ogg` + `fail.ogg` contract per pack, and remember pack names come straight from folder names on disk (spaces allowed). Audio overview: [[project_audio_model]].
