---
title: Evaluate Jev
status: draft
owner: nicktoper
agent: codex
workflow:
  name: draft-for-human
  steps:
  - name: agent-produces
    skills: []
    assignee: agent
  - name: human-owns-and-finishes
    skills: []
    assignee: owner
  - name: report-to-coga
    skills: []
    assignee: agent
step: 1 (agent-produces)
---

## Description

Find out whether Jev (TypeSafe AI's "System One" classifier model) can do
thinkpick's job well enough that we don't need to build our own classifier.
thinkpick recommends a thinking/effort level for a task description, and Jev
is a trendy, cheap, fast typed-choice classifier already used for a similar
purpose. If it just works, a lot of work disappears.

**First, verify Jev exists as described** (vendor, API, the `jcm-router`
prior art, the gist below). If it doesn't, stop and report that.

Then two parts, in order:

1. **Desk checks (can run anytime):** does the API accept examples, few-shot
   context, or tuning (decisive for per-project learning from corrections)?
   What is the "pitfall the vendor docs omit" mentioned in
   gist.github.com/pedramamini/014676fa8684d91bf7000f4623701ada? Do the
   speed (~250 ms) and cost (fraction of a cent) claims hold up, given they
   are mostly vendor-reported? Pricing, auth, rate limits, terms. How does
   `jcm-router` use it to pick Claude model + effort, and is there evidence
   about how well that works?
2. **Trial (gated on head-to-head):** run Jev over the labelled task set from
   `classifier/head-to-head-ground-truth` and score its picks with the
   shared scoring rule, against the always-medium baseline. Write down the
   label set and instruction you give Jev; they largely decide the result.
   If you can, also try it with a few labelled examples supplied. If the
   labelled set doesn't exist yet, finish the desk checks and
   `coga block` until it does: no unscored "impression" trial.

Owner touchpoint: `coga block --task classifier/evaluate-jev --reason "..."`
to get the Jev API key; never commit it.

Done means a report at `docs/research/jev-evaluation.md` with desk-check
findings (each sourced or marked unverified), trial setup and scored results,
and a verdict, **use as-is / use with changes / not good enough**, with the
reason. The trial script goes under `scripts/`; no product code. The owner
reviews and finishes it in the `human-owns-and-finishes` step.

## Context

- **Known about Jev:** returns a typed choice with a calibrated probability;
  it writes no text, so thinkpick's one-line reason would need a template.
- **Why per-project learning matters:** the right level depends on things
  the task text doesn't show (codebase, model, user's cost tolerance), so a
  backend that can't absorb corrections loses a key thinkpick feature. Weigh
  that in the verdict.
- **Scoring rule:** defined once in `docs/research/head-to-head.md`
  (from `classifier/head-to-head-ground-truth`): each labelled task has a
  "right level" (lowest level judged sufficient), and a pick is *right*,
  *under* (below it), or *over* (above it), with under-thinks weighted more.
  Use that rule; don't invent another.
- **Effort scale:** level names/count are undecided, owned by
  `_hold/decide-effort-scale-and-provider-mapping`. Use the provider's native
  low < medium < high as a placeholder ordinal scale and say so.
- **Data:** the owner approved (2026-10-08) sending real tasks to Jev and to
  a non-Claude judge. Files containing real task text stay out of git (put
  them under a gitignored `data/` and add the ignore rule). Commit only
  reports with aggregate results and redacted examples.
- Read `docs/research/state-of-the-art.md` first if it exists.
- **Order:** `classifier/state-of-the-art-review` →
  `classifier/head-to-head-ground-truth` (Jev desk checks may run in
  parallel) → `classifier/score-candidates` and the Jev trial →
  `decide-classifier-approach`. The owner may cancel any ticket an earlier
  result makes unnecessary (`coga mark canceled --message`).

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
