---
name: dpcat-prs-branches-sources
description: Pointers for the Riehl distillation — the three PRs on akshayshanker/DP-cat, the fork branches and commits behind them, the pinned sources, and a gh workaround.
metadata:
  type: reference
---

- **Upstream:** https://github.com/akshayshanker/DP-cat, `main` = `ca94dff` (25 Sep 2026);
  Pages site https://akshayshanker.github.io/DP-cat/.
- **Fork:** https://github.com/llorracc/DP-cat. Branches: `riehl-distillation` (`66abe12`,
  PR #1 head); `explainers` (`f4a6241`, PR #2 head); `reproduction-prompt` (`b09fcad`, PR #3
  head); `riehl-distillation-work` (this folder; deck identical to PR #1's).
- **PRs:** #1 https://github.com/akshayshanker/DP-cat/pull/1 (7 Oct 2026);
  #2 https://github.com/akshayshanker/DP-cat/pull/2 (10 Oct); #3
  https://github.com/akshayshanker/DP-cat/pull/3 (11 Oct).
- **Riehl,** *Category Theory in Context*, 2nd ed., https://emilyriehl.github.io/files/context.pdf,
  as used 7 Oct 2026: 299 pages, 1,948,866 bytes, SHA-256
  `e4779f34ff0432508f1105912f39a84af3c64799b969507325c80e27c2d994dd`.
- **Build:** Marp CLI 4.5.1; from the repo root,
  `marp --no-stdin free-category-of-a-dynamic-program.md --theme-set theme/econ-ark-cat.css -o deck.pdf --allow-local-files --html`;
  `tools/check-overflow.py` needs numpy and pillow.
- **`gh pr edit` fails on this repo** with a GraphQL `projectCards` deprecation error (11 Oct
  2026); use `gh api -X PATCH repos/akshayshanker/DP-cat/pulls/N -F body=@file` instead.
