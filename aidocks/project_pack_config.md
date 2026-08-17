---
name: project_pack_config
description: "Per-pack timing config via info.cmf (key=value, colon lists for per-difficulty); parsed by the cmf class in main/parsers/game_parser.nvgt into the packinfo global; every value falls back to the game default."
metadata:
  node_type: project
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16. Each Simon pack can ship an **`info.cmf`** file (`csm/sounds/simons/<pack>/info.cmf`) that a **pack author** uses to tune that pack's difficulty feel. This is NOT player save data — it's read-only config bundled with the pack. An empty or missing file, or any omitted/invalid key, falls back to the game's default, so old packs keep working unchanged.

**Format:** `key=value` per line; blank lines and lines starting with `#` or `;` are ignored; whitespace trimmed. Per-difficulty values are a single **colon-separated list**, order easy:medium:hard:insane (the dev chose colons over commas, and one list over four repeated keys).

**Keys + defaults** (all times in ms; consumed by the game):
- `playback_delay` = `500:400:300:200` — starting tone-gap per difficulty (`apply_difficulty`, index `difmod-1`).
- `reaction_delay` = `2000:1500:1000:500` — starting reaction window per difficulty (`apply_difficulty`).
- `ramp_step` = `5` — per-round shrink, multiplied by difmod (`next_round`).
- `playback_floor` = `100`, `reaction_floor` = `200` — the ramp's lower limits (`next_round`).
- `tone_padding` = `50` — fixed gap added after each tone (`play_sequence`: `wait(play_delay + tone_padding)`).
- `autopilot_risk` = `5`, `autopilot_factor` = `5` — autopilot fail chance = `risk*difmod + length/factor` (`game_loop`; `factor` clamped to ≥1 to avoid ÷0).

**Parser & wiring:**
- `main/parsers/game_parser.nvgt` defines `class cmf` (file was renamed from `cmf.nvgt`; class stays `cmf`). Methods: `load(path)`, `get_int(key, def)`, `get_list_int(key, index, def)` (splits on `:`), `get_string`, `has`. Validation uses `.is_digits()` + `string_to_number()` (from [[project_include_tree]]'s bgt_compat), so a bad value returns the default, never 0.
- The manifest `includes/includes.nvgt` globs `main/parsers/*` (added by the dev).
- Global `cmf packinfo;` in `dec.nvgt`. `load_simon_pack()` (`gamefuncts.nvgt`) calls `packinfo.load("sounds/simons/"+pack+"/info.cmf")` on each pack load.

**Design note:** every consumer passes the current hardcoded value as the `def` argument, so defaults live at each call site and match the old behavior exactly. See the difficulty ramp it configures, and [[project_game_modes]] for the mode system that runs alongside difficulty. `.cmf` is the pack config extension; distinct from the `.csg` save files ([[project_save_data_layout]]).
