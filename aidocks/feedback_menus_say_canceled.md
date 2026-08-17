---
name: feedback_menus_say_canceled
description: "REVERSED 2026-08-16: menus must NOT speak 'canceled' (or any cancel cue) on escape/Back/No. The dev wants nothing to say canceled. (Legacy slug; the rule is now the opposite of the name.)"
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

**Rule reversed on 2026-08-16.** This file used to say every escape/Back/No path should speak `"canceled"`. The dev has since said plainly: **"I don't want anything to say canceled."**

**Current rule:** menus and input flows are SILENT on cancel/back/No. Do NOT add `speak("canceled")` (or any cancel confirmation) to an escape / Back / No branch — just `return`. The filename slug still says "say_canceled" only so existing `[[links]]` keep resolving; the actual rule is the opposite.

**How to apply:** New/edited menus and input boxes return quietly on cancel — no spoken cue. Already removed from `settingsmenu()` and `viewscores()`. Two vendored deps still contain cancel speech in library flows — `custom_menu.nvgt` ("Cancelled." on a search cancel) and `speech.nvgt` ("Canceled" on input cancel) — left as-is unless the dev asks to silence those too. Related UI conventions: [[feedback_yes_no_menu_labels]].
