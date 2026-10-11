---
name: dpcat-reproduction-prompt-design
description: Chris's ruling (9 Oct 2026) on how a prompt that reproduces AI work is written — pin the inputs; state the first run's decisions, but tell the executor it should consider departing from each and record departures.
metadata:
  type: feedback
---

Chris, 9 Oct 2026: "You should definitely pin inputs, but I don't want you to spell out all of
your decisions and force the other AI to take them. Instead, the prompt should specify your
decisions but explicitly indicate that the AI is free to make different decisions as it thinks
appropriate." And: "instead of 'may' depart from, be more explicit that the executor 'should
consider' departing from."

**Why:** a prompt that dictates every decision reproduces the first run's mistakes along with its
results; one that pins inputs but opens decisions lets a second run improve on the first while
staying comparable with it.

**How to apply:** three parts, as in `prompt-reproduce-distillation.md` (PR #3): fixed
constraints and pinned inputs (commit, per-file checksums, document checksums, tool versions);
the first run's decisions with their reasons, each to be considered and departed from where
better, departures and the alternatives weighed recorded in a "Departures from the first run"
section; the bar that holds regardless. Do not point the executor at the first run's output, so
that a re-run is blind. Related: [[dpcat-prs-branches-sources]].
