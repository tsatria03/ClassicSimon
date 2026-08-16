---
name: project_save_data_layout
description: "AppData saves under tsatria03/ClassicSimon/saves/ use the .csg extension: settings.csg (st, readpreffs/writepreffs) and scores.csg (sc, record_score/score_line). NOT .tmg — that's ToyMania's."
metadata:
  node_type: memory
  type: project
  originSessionId: 31c6bf49-4057-4cc5-b5a7-1bc2c398767c
---

Added 2026-08-16, adapted from the ToyMania save system (the dev pointed at `ToyMania` as the reference). Two save files, both in `DIRECTORY_APPDATA + "tsatria03/ClassicSimon/saves/"`: **settings** and **scores**.

**File extension is `.csg`** (ClassicSimon Game), NOT ToyMania's `.tmg` — the dev renamed it deliberately. Don't reintroduce `.tmg` here.

**The `savedata` class** (`main/deps/savedata.nvgt`, by Ultrocity Audio / Mason) is a dictionary-backed key/value store: constructor `savedata(filename, enckey="")`; `load()` (no-ops if the file is missing), `save()` (serializes `d`, encrypts only if a key is set); overloaded `add(name, value)` for bool/double/float/int/int64/string; typed readers `read_bool/read_double/read_float/read_int/read_int64/read_string`; public dictionary `d`, so reads are guarded with `st.d.exists("key")`.

**Two `savedata` instances** in `main/globals/dec.nvgt`: `st` → `.../saves/settings.csg` and `sc` → `.../saves/scores.csg`. A sibling `string savePath = DIRECTORY_APPDATA + "tsatria03/ClassicSimon/saves"` is the directory used for `directory_create` (both files live in it). **Both files are now ENCRYPTED — the dev added long per-file keys (the `savedata` 2nd arg) on 2026-08-16.** These keys are set by the dev; NEVER regenerate, shorten, or hand-edit a key — changing it orphans every existing save (the old file can no longer be decrypted). If asked to touch the keys, confirm first and warn about orphaning. (Note: a save written while a key was blank is plaintext and will fail to decrypt after the key is added — delete stale files in `saves/` once when switching from blank to keyed.)

**The functions** — `readpreffs()`/`writepreffs()` in `main/functions/savefuncts.nvgt`. `readpreffs()` is called at the top of `csm.nvgt` `main()`'s else block (before the `if(logoplay)` logo check, so a saved value takes effect that launch); it does `st.load()` then guarded reads into `logoplay` and `simspeech`. `writepreffs()` ensures `savePath` exists via `directory_create` (the class's `save()` uses `f.open(...,"wb")`, which does NOT create folders — so the dir must exist first), `st.add`s both keys, and `st.save()`s. `settingsmenu()` calls `writepreffs()` after every toggle. See [[project_settings_menu]].

**Settings file (`st` → settings.csg)** persists `menumusvolume`, `logoplay`, and `simspeech` (guarded `st.d.exists` reads). `difmod`/`autopilot`/`simontype`/round state stay per-session.

**Scores file (`sc` → scores.csg)** holds the best single-game score per pack per difficulty. See [[project_score_tracking.md]] — `record_score()`/`score_line()` in `main/functions/scorefuncts.nvgt`, keyed `"best_" + pack + "_" + difmod`. Both `readpreffs()`/`writepreffs()` (settings) and `record_score()` (scores) call `directory_create(savePath)` before saving, and `record_score()`/`viewscores()` `sc.load()` fresh each time — so ClassicSimon needs no startup save-load wiring in `main()` (unlike ToyMania, which loads + makes dirs in `main()`).
