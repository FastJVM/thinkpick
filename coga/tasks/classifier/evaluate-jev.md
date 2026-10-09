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

The ticket has two parts:

1. **Desk checks (can run anytime):** does the API accept examples, few-shot
   context, or tuning (decisive for per-project learning from corrections)?
   What is the "pitfall the vendor docs omit" mentioned in
   gist.github.com/pedramamini/014676fa8684d91bf7000f4623701ada? Do the
   speed (~250 ms) and cost (fraction of a cent) claims hold up, given they
   are mostly vendor-reported? Pricing, auth, rate limits, terms. How does
   `jcm-router` use it to pick Claude model + effort per message, and is
   there any evidence about how well that works?
2. **Hands-on trial:** run Jev over real tasks and score its picks.
   Scoring needs ground truth, which comes from
   `classifier/head-to-head-ground-truth`. If that ticket's labelled task
   set exists, score against it. If not, run the trial anyway on ~20 real
   tasks and have the owner rate each pick *too little / right / too much*,
   clearly labelled as an impression, not a measurement.

Done means a report at `docs/research/jev-evaluation.md` with the desk-check
findings (each sourced or marked unverified), the trial setup and results,
and a verdict: **use as-is / use with changes / not good enough**, with the
reason. Include the minimal code/script used for the trial under
`scripts/` or inline; no product code. The owner reviews and finishes it in
the `human-owns-and-finishes` step.

## Context

- **Known about Jev:** returns a typed choice with a calibrated probability;
  it writes no text, so thinkpick's one-line reason would need a template.
  Prior art: `jcm-router` (see github.com/yibie/awesome-jev).
- **Needs an API key**, which the PoC accepts. Ask the owner for it; don't
  commit it (use `coga.local.toml` / env vars).
- **Effort scale:** thinkpick's level names and count are undecided (owned by
  a future scale ticket). Use a placeholder ordinal scale, e.g.
  low < medium < high, and say so.
- **Why per-project learning matters:** the right level depends on things
  the task text doesn't show (codebase, model, user's cost tolerance), so a
  backend that can't absorb corrections loses a key thinkpick feature.
  Weigh that in the verdict.
- Read `docs/research/state-of-the-art.md` first if it exists (from
  `classifier/state-of-the-art-review`).
- Output feeds `decide-classifier-approach` (final build-or-buy decision).

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
