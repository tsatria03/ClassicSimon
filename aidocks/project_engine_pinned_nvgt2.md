---
name: project_engine_pinned_nvgt2
description: "ClassicSimon runs on the pinned legacy NVGT fork at C:\\nvgt (BASS audio); the miniaudio build would be C:\\nvgt2 (not installed) — don't target it or suggest upgrading."
metadata:
  node_type: memory
  type: project
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

The game is pinned to the **legacy NVGT fork at `C:\nvgt\nvgt.exe`** (BASS audio). The launcher `csm/csm.py` and `build/tools.py` both use it. The miniaudio build of NVGT would live at `C:\nvgt2` (only for testing other people's games; not currently installed) — **do not target it or treat upstream NVGT as authoritative**, and don't suggest upgrading the engine.

**Why:** ClassicSimon depends on behavior specific to the nvgt fork; upstream NVGT changed the audio backend and other internals and is incompatible.

**Notes:**
- Unlike the sibling games, ClassicSimon has **no in-code relaunch/restart** — nothing hardcodes a `nvgtw.exe` path.
- (History: the legacy fork was at `C:\nvgt2` before a 2026-08-15 rename; it's now `C:\nvgt`. The engine name/slug "nvgt2" is a historical identifier.)
- The nvgt stdlib includes live under `C:\nvgt` and in the source mirror at `misc\Legacy-NVGT\release\include`; `csm/lib` bundles the runtime DLLs (BASS/Tolk/nvgt_curl/phonon…) for compiled builds — see [[project_build_pipeline]].
