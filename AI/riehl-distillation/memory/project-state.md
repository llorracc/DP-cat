---
name: dpcat-riehl-project-state
description: State of the Riehl distillation as of 2026-10-11 — three open PRs on akshayshanker/DP-cat, what sits on which fork branch, what is held back and why.
metadata:
  type: project
---

As of 2026-10-11 09:27 EDT. Fork `llorracc/DP-cat` (public); Chris's clone is checked out on
`riehl-distillation-work`, the branch that carries this folder.

- **PR #1** (deck and verification notes; fork branch `riehl-distillation`, `66abe12`), **PR #2**
  (the two explainers as `explainers/`; branch `explainers`, `f4a6241`), **PR #3** (the
  reproduction prompt as `prompts/`; branch `reproduction-prompt`, `b09fcad`): all cut from
  upstream `main` `ca94dff`, all open, none answered on GitHub.
- **Akshay's response** is by email (9 Oct): hold slides for Riehl's group until he and Chris
  have talked; he wants input into their construction. Chris agreed by email on 11 Oct.
- **This branch** holds the working records under `AI/riehl-distillation/`, force-added because
  upstream ignores `AI/`. Its deck text is identical to PR #1's. The Spivak–Topos placements
  (memo §3, six slides) are drafted and not applied.
- **Held back from the public fork:** `model-files-bellman-sym-vs-open-game-engine.md`, which
  quotes a private repository's files; its home is that repository.

**Why:** a session resuming here otherwise has to rediscover which of four branches carries what
and whether Akshay has answered.

**How to apply:** check the three PRs and Chris's mail for replies before doing anything else;
make deck changes on this branch and cherry-pick onto `riehl-distillation` only when Chris says
to refresh PR #1. Related: [[dpcat-akshay-constraints]], [[dpcat-prs-branches-sources]].
