---
name: project_repo_hygiene
description: ".gitattributes enforces LF here (NOT CRLF like the sibling games); gitignore hides *.ini/lib/releases (tools.ini un-ignored); CLAUDE.md + aidocks committed."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

**Line endings — this repo is LF.** `.gitattributes` is `* text=auto eol=lf`, so all text files are normalized to **LF**. This is DIFFERENT from the other tsatria03 games (ClassicStand/CookieCraze/ToyMania/SimpleFighter), which enforce CRLF. Create new files here as LF; don't carry the CRLF habit over ([[feedback_no_crlf_normalization]]). Binary types (`*.exe/*.ogg/*.mp3/*.wav/*.zip/*.dat`) are marked binary.

**Gitignored:** `*.ini` (so `build/tools.ini` is force-tracked via a `!build/tools.ini` negation), `lib/` (the `csm/lib` runtime DLLs live on disk but aren't committed), `releases/`, and `New File*.txt`.

**Committed on purpose:** `CLAUDE.md` and the whole `aidocks/` folder (this memory store).
