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
proof-of-concept stage, and before building anything the owner wants to know
whether the literature or existing tools already answer this. This is the
first and cheapest `classifier/*` ticket; its result may make the others
unnecessary.

Time-box it: roughly 10–20 well-chosen sources and a 3–6 page report, depth
over breadth.

Done means one markdown report at `docs/research/state-of-the-art.md` (create
the directory) that:

1. Covers the relevant prior work: academic work on adaptive compute /
   reasoning-budget prediction / difficulty estimation, LLM routers (model or
   effort routing), and practical tools (provider "auto" effort modes,
   `jcm-router` at landscape level only: Jev details belong to
   `classifier/evaluate-jev`). For each: what it predicts, from what input,
   how it was evaluated, reported results, link.
2. Says what works and what does not, and in particular **how prior work gets
   ground truth** for "the right level" (thinkpick's core unknown).
3. Notes anything on learning from per-user/per-project feedback, and any
   judging protocol (human or LLM-as-judge) worth copying.
4. Ends with a verdict, **solved / partly solved / open**, and what it
   implies: reuse as-is, adapt a known method, or build our own. Say what
   `classifier/head-to-head-ground-truth` and `classifier/evaluate-jev`
   should take from it, and whether either looks unnecessary.

Mark every claim as sourced (link) or your inference; label vendor-reported
numbers. The owner reviews, finishes, and decides any sibling skips in the
`human-owns-and-finishes` step.

## Context

- **Core unknown:** we have no ground truth. The "right" level is the
  lowest one that would have been good enough, which nobody observes
  directly. Any method claiming to predict it must have measured it somehow;
  that is the most useful thing to extract.
- **Why per-project learning matters:** the right level depends partly on
  the task text and partly on things the text doesn't show: codebase size
  and tangledness, which model runs the task, the user's quality/cost
  tolerance. thinkpick wants to learn from "too little / right / too much"
  feedback from day one.
- **Feedback bias:** users report "too shallow" far more readily than "less
  would have worked". Note any prior work that addresses this.
- Prior-art pointer for the Jev ecosystem: github.com/yibie/awesome-jev.
- **Order:** `classifier/state-of-the-art-review` →
  `classifier/head-to-head-ground-truth` (Jev desk checks may run in
  parallel) → `classifier/score-candidates` and the Jev trial →
  `decide-classifier-approach`. The owner may cancel any ticket an earlier
  result makes unnecessary (`coga mark canceled --message`).
- Out of scope: writing code, running experiments, picking providers.

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
