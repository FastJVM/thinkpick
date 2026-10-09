---
title: State of the art review
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

Survey the state of the art: is predicting how much reasoning effort a model
needs, from the task description alone, already a solved problem? thinkpick
(a small CLI that recommends a thinking/effort level for a task) is at
proof-of-concept stage, and before building a classifier the owner wants to
know whether the literature or existing tools already answer this. This is
the first of the `classifier/*` tickets and the cheapest; its result may make
the others unnecessary.

Done means one markdown report at `docs/research/state-of-the-art.md` (create
the directory) that:

1. Covers the relevant prior work: academic work on adaptive compute /
   reasoning-budget prediction / difficulty estimation, LLM routers (model or
   effort routing), and practical tools (e.g. `jcm-router`, provider
   "auto" effort modes). For each: what it predicts, from what input, how it
   was evaluated, and reported results, with links.
2. Says what works and what does not, and in particular **how prior work gets
   ground truth** for "the right level" (this is thinkpick's core unknown;
   see Context).
3. Notes anything on learning from per-user/per-project feedback.
4. Ends with a verdict, **solved / partly solved / open**, and what it
   implies for thinkpick: reuse something as-is, adapt a known method, or
   build our own. Flag what the sibling tickets `classifier/evaluate-jev` and
   `classifier/head-to-head-ground-truth` should take from it (e.g. a judging
   method or evaluation protocol worth copying).

Mark every claim as sourced (link) or your inference. Vendor-reported numbers
are labelled as such. The owner reviews and finishes the report in the
`human-owns-and-finishes` step.

## Context

- **Core unknown (why this ticket exists):** we have no ground truth. The
  "right" level is the lowest one that would have been good enough, which
  nobody observes directly. Any method that claims to predict it must have
  measured it somehow; that is the most useful thing to extract.
- **Why per-project learning matters:** the right level depends partly on
  the task text and partly on things the text doesn't show: codebase size
  and tangledness, which model runs the task, the user's quality/cost
  tolerance. thinkpick wants to learn from "too little / right / too much"
  feedback from day one.
- **Feedback bias:** users report "too shallow" far more readily than "less
  would have worked". Note any prior work that addresses this.
- **Jev** (TypeSafe AI's "System One" classifier model) gets its own ticket,
  `classifier/evaluate-jev`; mention it here only as part of the landscape.
  Prior-art pointer: github.com/yibie/awesome-jev.
- Output feeds `decide-classifier-approach`, which makes the final
  build-or-buy decision after all `classifier/*` tickets.
- Out of scope: writing code, running experiments, picking providers.

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
