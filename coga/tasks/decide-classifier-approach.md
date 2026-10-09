---
title: Decide classifier approach
status: draft
owner: nicktoper
agent: codex
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

Make the build-or-buy decision for how thinkpick picks an effort level, from
the evidence produced by the three `classifier/*` tickets. Run this **last**,
after `classifier/state-of-the-art-review`, `classifier/evaluate-jev`, and
`classifier/head-to-head-ground-truth` are done (or explicitly skipped
because an earlier result settled the question). If any input report is
missing, say which, and ask the owner whether to proceed without it.

Inputs: `docs/research/state-of-the-art.md`, `docs/research/jev-evaluation.md`,
`docs/research/head-to-head.md`, and the labelled task set from the
head-to-head ticket.

Done means one markdown document at `docs/decisions/classifier-approach.md`
(free-form, no ADR template) containing:

1. **Decision:** one of: reuse an existing system (e.g. Jev, a published
   method), build our own (and which backend: cheap LLM call with logged
   corrections as in-prompt examples, heuristics, or a hybrid), or stop
   (the problem isn't worth solving, e.g. the cheaper level is almost always
   fine). Cite the evidence for it.
2. **Interface**, if building or wrapping: task text (+ project id) in →
   ordered level + confidence + one-line reason out, and how per-project
   corrections feed in (e.g. last N corrections as examples, with a cap).
3. **Alternatives set aside:** heuristics, cheap LLM call, Jev, published
   methods, and hybrids, each assessed on pick quality *as measured against
   the head-to-head set where possible*, cost, latency, offline use, and
   ability to absorb per-project/user feedback. For each, say when it would
   become the right choice.
4. **Ongoing check:** how the chosen approach keeps being scored once in use
   (e.g. *too little / right / too much* feedback compared with the
   head-to-head set; pass = beats an "always the middle level" baseline and
   improves as corrections accumulate). Propose the log fields as a
   suggestion for a future feedback-log ticket, not a binding schema.
5. **Open questions**, each pointing to an owning ticket or marked
   *unowned, proposed new ticket*.

The owner reviews and finishes it in the `human-owns-and-finishes` step.

## Context

- **History:** this ticket originally chose "cheap LLM call + in-prompt
  corrections" by reasoning alone. The owner rejected that (2026-10-08):
  with no ground truth we can't tell good from bad, so the evidence
  tickets come first. The LLM approach is still the leading build
  candidate. It is easy to build, writes a real reason sentence, and gets
  in-prompt learning almost free. But it is no longer the default.
- **Why learning matters:** the right level depends partly on the task and
  partly on things the text doesn't show: codebase size and tangledness,
  which model runs the task, and the user's quality/cost tolerance.
- **Feedback bias:** users report "too shallow" far more readily than "less
  would have worked". The three-way answer, logged with the level actually
  used, mitigates but doesn't remove this.
- **Scale dependency:** the exact level names/count and the mapping to
  provider knobs belong to a future scale ticket. Assume a placeholder
  ordinal scale and say so.
- Out of scope: writing code, picking providers, packaging.

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
