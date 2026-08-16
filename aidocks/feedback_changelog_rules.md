---
name: feedback_changelog_rules
description: "csm/docks/changelog.txt is a player-facing record of what changed: prose, reverse-chronological, bump version.txt with each block."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

`csm/docks/changelog.txt` is the source of truth for what shipped, and it is a **record of what changed, not a manual**. Player-facing prose, one idea per entry, reverse-chronological (newest block on top). Each new version block pairs with a bump of `build/version.txt` to the same number (see [[feedback_update_build_version_txt]]).

**Do NOT open a new version block or bump `version.txt` on your own.** While a version is still in development, add new entries to the **top of the current (in-progress) version block** — the same block, not a new one. A version bump happens only when the dev explicitly says that version is done / a release is being cut. When unsure which version is current, ask or assume it's the topmost block; never invent the next number. (Corrected 2026-08-16: added 1.0 entries under the existing "New in 1.0." block after wrongly creating a "New in 1.1." block — the dev was "not done with 1.0 yet.")

**Why:** Players read the changelog; engine internals, file names, and how-to instructions don't belong there. The changelog + `todo_list.txt` are the durable human record of the project's evolution.

**How to apply:** Describe the observable change in a sentence or two. Trust the changelog over the readme/todo when they disagree. Keep lines within the dock line limit ([[feedback_dock_line_length_1024]]).
