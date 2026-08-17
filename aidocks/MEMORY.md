# ClassicSimon memory index

The `[[name]]` links in `CLAUDE.md` and across these memories resolve to `aidocks/<name>.md`. Add a one-line pointer here for every new memory. "Memory" / "memories" always means this folder. ClassicSimon is a collab/mirror of Keri-marie Kelly's Simon game; it's LIGHTER than the sibling games (LF line endings, no installer/website/password). Its includes were reorganized on 2026-08-16 from flat to the sibling `src/includes/main/{deps,functions,globals,menus}/` nested layout, pulled by an `includes/includes.nvgt` manifest.

## Project — what the game is and how it's built
- [Game vision](project_game_vision.md) — audio Simon memory game: pick a sound pack + difficulty, memorize and repeat a growing tone sequence; a collab/mirror of Keri-marie Kelly's game; state is in-memory only.
- [Simon pack system](project_simon_pack_system.md) — selectable packs under csm/sounds/simons/<pack>/ (1..4.ogg + fail.ogg); find_directories enumerates, load_simon_pack loads via find_sound (random pick).
- [Path conventions](project_path_conventions.md) — src/ (code) + csm/ (assets+launcher) + build/ + releases/ split; the cwd=csm/ trick; the (now-removed) #pragma asset caveat; no data/ folder.
- [Include tree](project_include_tree.md) — NESTED src/includes/main/{deps,functions,globals,menus}/ pulled by the includes/includes.nvgt manifest; slim csm.nvgt; globals in dec.nvgt; version.nvgt provides the version; rotation backs sound_pool.
- [Build pipeline](project_build_pipeline.md) — csm.py launcher + build/tools.py (SimpleFighter-based, NO installer / NO website / NO password); version mirroring; ASSET_FOLDERS=docks,lib,sounds.
- [Audio model](project_audio_model.md) — 4 tones + fail as direct `sound` objects (not spatial); custom_menu's own pool for menus; clips cwd-relative to csm/.
- [Repo hygiene](project_repo_hygiene.md) — .gitattributes enforces LF here (NOT CRLF like the siblings); gitignore hides *.ini/lib/releases; tools.ini un-ignored; CLAUDE.md + aidocks committed.
- [Engine pinned to nvgt2](project_engine_pinned_nvgt2.md) — runs on the legacy fork at C:\nvgt (BASS); miniaudio would be C:\nvgt2 (not installed); no in-code relaunch; don't target it or suggest upgrading.

- [Settings menu](project_settings_menu.md) — Play/Settings/Exit; settingsmenu() holds on/off toggles for "Play logo at startup" (logoplay) and "Spoken hints" (simspeech), with per-item focus tracking; both now persist across launches.
- [Save data layout](project_save_data_layout.md) — AppData saves under tsatria03/ClassicSimon/saves/ use the .csg extension (NOT ToyMania's .tmg): settings.csg (st: menumusvolume/logoplay/simspeech) and scores.csg (sc); both files encrypted with dev-set per-file keys (never regenerate a key — it orphans existing saves); no startup load wiring (loads are lazy).
- [Score tracking](project_score_tracking.md) — best single-game score per pack per difficulty, saved to scores.csg via record_score/score_line (scorefuncts.nvgt); recorded in game_over when !autopilot ("New best!" only when beating an existing record); View scores menu in each pack's menu.

- [custom_menu run() index bases](project_custom_menu_run_indexing.md) — run() RETURNS 1-based (current_item+1) and get_item_id expects that, but run()'s start_position arg is 0-based; subtract 1 when feeding a returned index back as start_position (bit the settings menu focus).

## NVGT / AngelScript gotchas — these cause compile failures (game won't launch)
- [AngelScript braceless if](project_angelscript_braceless_if.md) — a braceless if/else governs one statement; a second orphans the else → compile error.
- [AngelScript reserved words](project_angelscript_reserved_words.md) — never name a variable `out` (or in/inout/shared/final/from…); reserved keyword → compile error.
- [NVGT key_pressed one-shot](project_nvgt_key_pressed_oneshot.md) — key_pressed() is consumed on first read each frame; read a multi-purpose key once and branch inside.
- [NVGT sound preload cache](project_nvgt_sound_preload_cache.md) — sound.load caches by filename; reusing a name for changed audio replays the old clip; use a fresh name or allow_preloads=false.

## Feedback — how the dev wants you to work
- [Confirm before implementing](feedback_confirm_before_implementing.md) — a design discussion or any `?` is a request for a plan, not a green light to edit; wait for explicit go-ahead.
- [Ask one question at a time](feedback_ask_one_question_at_a_time.md) — surface ONE question per turn and wait; don't batch a numbered list.
- [List modified files](feedback_list_modified_files.md) — end every editing turn with a bare-filename "Files changed:" list; then note whether a relaunch is needed.
- [Don't run or build the game](feedback_dont_run_or_build_the_game.md) — never launch/compile (csm.py, tools.py, nvgt -c); edit and report, the dev runs and verifies.
- [Verify code while fixing](feedback_verify_code_while_fixing.md) — re-locate by symbol not line number, confirm the finding is true, flag adjacent bugs.
- [Check git log for commits](feedback_check_git_log_for_commits.md) — the dev commits between turns; check git log/status before assuming commit state; don't commit unless asked.
- [Stage commits before big changes](feedback_stage_commits_before_big_changes.md) — flag a commit break point before a risky stage so safe pieces land first.
- [CLAUDE.md length limit](feedback_claudemd_length.md) — keep CLAUDE.md a dispatcher under 40,000 chars; move detail into memory files.
- [Changelog rules](feedback_changelog_rules.md) — docks/changelog.txt: player-facing prose, reverse-chronological, a record not a manual; bump version.txt with each block.
- [Dock line length 1024](feedback_dock_line_length_1024.md) — keep every line in csm/docks/ at or under 1024 chars; the screen reader splits longer lines.
- [One-sentence game messages](feedback_one_sentence_game_messages.md) — in-game spoken feedback is exactly one sentence; no trailing advice.
- [Menus stay silent on cancel](feedback_menus_say_canceled.md) — REVERSED 2026-08-16: menus/input must NOT speak "canceled" on escape/Back/No; the dev wants nothing to say canceled (legacy filename slug).
- [Yes/no menu labels](feedback_yes_no_menu_labels.md) — label items exactly Yes/No (Yes first); context goes in the prompt.
- [Don't modify dep includes](feedback_dont_modify_deps.md) — never edit files under src/includes/main/deps/ (custom_menu, speech, savedata, etc.); they're vendored/read-only, work around them in game code.
- [Multiline comment style](feedback_multiline_comment_style.md) — multi-line comments use one /* */ block, not stacked //.
- [Don't flag indentation](feedback_dont_flag_indentation.md) — AngelScript ignores indentation; don't flag whitespace or reformat.
- [No CRLF normalization](feedback_no_crlf_normalization.md) — don't run a manual normalizer; preserve each file's ending. NOTE this repo is LF, unlike the CRLF siblings.
- [Update build/version.txt](feedback_update_build_version_txt.md) — version.txt is the single source of truth; never hand-edit the generated version.nvgt.
