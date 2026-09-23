---
title: Decide classifier approach
status: draft
owner: nicktoper
contexts:
  - product/vision
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

Write the decision record for how thinkpick makes its pick, plus the
feasibility test that tells the owner whether the idea works at all.
thinkpick is at proof-of-concept stage: the first question is whether the
right effort level can be predicted from a task description, and whether the
prediction improves with per-project/user corrections.

The owner's working decision, reached in the authoring interview, is:
**a cheap LLM call, with the owner's recent logged corrections included in
the prompt as examples** ("learning from day one" with no training step).
This ticket turns that into a short record the owner can approve or overturn;
it does not build anything.

Done means one markdown document (at `docs/decisions/classifier-approach.md`
unless the owner says otherwise) containing:

1. **Decision:** the approach above, and the interface every backend
   implements: task text (+ project id) in → ordered level + confidence +
   one-line reason out. Include a sketch of the prompt shape and how examples
   are selected (e.g. last N corrections for this project, plus a
   too-many-examples cap).
2. **Why, and the alternatives set aside:** heuristics, cheap LLM call, Jev,
   and hybrids, each assessed on pick quality, cost, latency, offline use,
   and *ability to absorb per-project/user feedback*. For each one set
   aside, say when it would become the right choice.
3. **Feasibility test protocol:** the owner runs the PoC on ~30 real tasks
   and marks each pick *too little / right / too much*. Define the baseline
   (constant "always medium", or whatever the middle level is), the pass
   criterion (clearly beats the baseline *and* improves as corrections
   accumulate, e.g. first 15 vs last 15), what a fail means, and the exact
   fields to log per pick.
4. **Open questions** the PoC does not answer, pointed to the ticket that
   owns them.

The owner reviews and finishes it in the `human-owns-and-finishes` step.

## Context

- **Why not an experiment to choose the backend:** there is no ground truth
  yet. The "right" level is the lowest one that would have been good enough,
  which nobody observes; finding it means running tasks at several levels,
  which is automated benchmarking and out of scope. So the backend is chosen
  by reasoning, and only feasibility is tested. The LLM backend is chosen
  because it is the easiest to build (a prompt, no rules to design), writes
  a real reason sentence, and gets in-prompt learning almost for free. An API
  key is acceptable; offline is not required for the PoC.
- **Why learning matters:** the right level depends partly on the task itself
  (general) and partly on things a classifier cannot see from the text:
  codebase size and tangledness, which model will run the task, and the
  user's quality/cost tolerance. Hence per-project/user corrections.
- **Feedback bias:** users report "too shallow" far more readily than "less
  would have worked". A three-way *too little / right / too much* answer,
  logged with the level that was actually used, mitigates this. The protocol
  should acknowledge the remaining bias.
- **Jev** (TypeSafe AI's "System One" classifier model): returns a typed
  choice with a calibrated probability in ~250 ms at a fraction of a cent;
  it writes no text, so the reason would need a template. Prior art:
  `jcm-router` uses it to pick Claude model + effort per message (see
  github.com/yibie/awesome-jev). Unverified: whether its API accepts
  examples/tuning (decisive for per-project learning), and a "pitfall the
  vendor docs omit" mentioned in gist.github.com/pedramamini/014676fa8684d91bf7000f4623701ada.
  Vendor speed/cost numbers are mostly self-reported. Treat Jev as a later
  backend, not the PoC one; note these checks in the record.
- **Heuristics:** free, offline, deterministic, easy to tune per project via
  config; but someone must design the rules and reason templates, so they're
  more work than an LLM prompt for a PoC.
- **Scale dependency:** the classifier outputs an *ordered* level; its exact
  names/count and the mapping to token budgets or provider knobs belong to
  `decide-effort-scale-and-provider-mapping`. Don't block on it: assume a
  placeholder ordinal scale and say so.
- **Related tickets that build what this decides:** `recommendation-core-library`
  (the core and backend interface) and `local-was-it-right-feedback-log`
  (the correction log). Write the record so those designs can cite it.
- Out of scope: writing code, running the feasibility test, picking
  providers, packaging.

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
