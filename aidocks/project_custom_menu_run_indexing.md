---
name: project_custom_menu_run_indexing
description: "custom_menu.run() RETURNS a 1-based index (current_item+1); get_item_id expects that, but run()'s start_position arg is 0-based — subtract 1 when feeding a returned index back as start_position."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

**`custom_menu` (`main/deps/custom_menu.nvgt`) mixes index bases — a genuine off-by-one trap.**

- `int run(int start_position = 0, bool start_focused = false, bool speak_focused = false)` treats `start_position` as **0-based** — it does `current_item = start_position` (line ~248). Item 0 is the first item.
- But on Enter, `run()` **returns `current_item + 1`** (line ~462), i.e. a **1-based** index. Escape/cancel returns `-1`.
- `get_item_id(res)` is written to expect that 1-based return value, so `m.get_item_id(m.run())` and id checks (`setid == "logo"`) work correctly with the raw return value — do NOT adjust `res` before calling `get_item_id`.

**The trap:** if you save a returned index and feed it back as `start_position` to re-focus the same item (e.g. a settings menu that re-opens on the item just toggled), you MUST convert 1-based → 0-based first: `focus = res - 1;`. Passing `res` directly lands focus one item too far down. This exact bug hit `settingsmenu()` — toggling "Play logo at startup" (res 1) jumped focus to "Spoken hints" (index 1) until the `- 1` was added. See [[project_settings_menu]].
