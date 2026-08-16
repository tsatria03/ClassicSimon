---
name: feedback_no_crlf_normalization
description: "Don't run a manual line-ending normalizer; preserve each file's ending on edit. This repo is LF (unlike the CRLF-enforced sibling games)."
metadata:
  node_type: memory
  type: feedback
  originSessionId: 9c2aa66a-3d36-4ec3-8742-42265704afb7
---

Don't run a manual line-ending normalizer over the tree. When you edit a file, preserve its existing line ending. Line-ending policy is declared in `.gitattributes` and applied by git at commit time — see [[project_repo_hygiene]].

**Why:** A blind normalizer pass flips endings on files you didn't mean to touch, producing huge noisy diffs and fighting the `.gitattributes` policy.

**How to apply — NOTE this repo differs from the sibling tsatria03 games:** ClassicSimon's `.gitattributes` is `* text=auto eol=lf`, so it is **LF**, not CRLF. New files you create (`.nvgt`, `.py`, `.md`, etc.) should be **LF**. (The other games — ClassicStand/CookieCraze/ToyMania/SimpleFighter — enforce CRLF; don't carry that habit over here.)
