---
name: project_save_data_layout
description: "Persistent settings live in AppData: DIRECTORY_APPDATA + tsatria03/ClassicSimon/saves/settings.tmg, via the savedata st instance + readpreffs/writepreffs."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16, adapted from the ToyMania save system (the dev pointed at `ToyMania` as the reference). ClassicSimon has **no game-state/stats save** — only a **settings** file so far.

**The `savedata` class** (`main/deps/savedata.nvgt`, by Ultrocity Audio / Mason) is a dictionary-backed key/value store: constructor `savedata(filename, enckey="")`; `load()` (no-ops if the file is missing), `save()` (serializes `d`, encrypts only if a key is set); overloaded `add(name, value)` for bool/double/float/int/int64/string; typed readers `read_bool/read_double/read_float/read_int/read_int64/read_string`; public dictionary `d`, so reads are guarded with `st.d.exists("key")`.

**ClassicSimon's instance** — `savedata st(...)` in `main/globals/dec.nvgt`, pointing at `DIRECTORY_APPDATA + "tsatria03/ClassicSimon/saves/settings.tmg"`. A sibling `string savePath = DIRECTORY_APPDATA + "tsatria03/ClassicSimon/saves"` is the directory used for `directory_create`. **The encryption key is currently blank (`""`, plaintext)** — the dev plans to fill it in; leave it blank unless told otherwise. (Plaintext fits the game's "no password / nothing to hide" ethos, but the dev may still add a key.)

**The functions** — `readpreffs()`/`writepreffs()` in `main/functions/savefuncts.nvgt`. `readpreffs()` is called at the top of `csm.nvgt` `main()`'s else block (before the `if(logoplay)` logo check, so a saved value takes effect that launch); it does `st.load()` then guarded reads into `logoplay` and `simspeech`. `writepreffs()` ensures `savePath` exists via `directory_create` (the class's `save()` uses `f.open(...,"wb")`, which does NOT create folders — so the dir must exist first), `st.add`s both keys, and `st.save()`s. `settingsmenu()` calls `writepreffs()` after every toggle. See [[project_settings_menu]].

**Only `logoplay` + `simspeech` are persisted.** Other in-memory globals (`difmod`, `autopilot`, `simontype`, game state) remain per-session. ToyMania (the reference) additionally keeps a `sd` stats instance and creates its dirs in `main()` rather than inside the save function — ClassicSimon self-contains the `directory_create` inside `writepreffs()` instead.
