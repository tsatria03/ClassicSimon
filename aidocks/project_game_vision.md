---
name: project_game_vision
description: "ClassicSimon is an audio Simon (memory) game — pick a sound pack + difficulty, then memorize and repeat a growing tone sequence; a collab/mirror of Keri-marie Kelly's game."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

ClassicSimon is an audio-only (blind-accessible) **Simon memory game**: the game plays a growing sequence of the four tones, and the player repeats it with the arrow keys — one more tone is added each round until they miss. It's a **collaboration/mirror**: the original game was made by the user's friend **Keri-marie Kelly**, who gave the user permission to extend it (so it's not originally the user's — hence, e.g., no password on releases).

**Load-bearing systems (the game's identity):**
- **Sound packs** = selectable instruments (`sounds/simons/<pack>/1..4.ogg` + `fail.ogg`) — [[project_simon_pack_system]].
- **Input:** the four arrow keys map to tones 1-4 (`get_pressed_tone()`); the sequence loop and per-round timing live in `game.nvgt` / `next_round()` / `play_sequence()`.
- **Difficulty:** easy/medium/hard set the play delay + input timeout (`apply_difficulty()`, `difmod`); delays tighten each round.
- **Modes/menu:** Play → pick pack → Start game or **Learn tones** (a free-play tone browser, `learnsounds()`); plus an **autopilot** option and a `simspeech` toggle for spoken tone names.
- **State is in-memory only** — no save/prefs system; settings reset each launch.

Version is `1.0` (in `src/includes/version.nvgt`). Player-facing docs live in `csm/docks/` (changelog/credits/readme).
