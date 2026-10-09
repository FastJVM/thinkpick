---
title: Head-to-head ground truth
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

Produce the ground truth thinkpick is missing, by running real tasks at
several effort levels and judging whether the cheaper level was good enough.
Without this no classifier (ours, Jev, or a published one) can be scored:
the "right" level is the lowest one that would have sufficed, and nobody
observes it unless they compare. thinkpick is a small CLI that recommends a
thinking/effort level for a task.

Scope it as a small, mostly manual study, **not** an automated benchmarking
harness:

1. **Protocol:** ~20–30 real tasks from the owner, **answerable in a single
   API call** (no agentic coding runs in v0; say what that leaves out). Run
   each on one Claude model via the API at **low, medium, and high**. The
   owner judges two blind, randomly ordered pairs per task, low vs medium and
   medium vs high: *cheaper was good enough / higher was needed / both
   inadequate*. Define "good enough" per task type.
2. **Scoring rule (shared by later tickets):** a task's *right level* is the
   lowest level judged sufficient. Say how inconsistent pairs and "both
   inadequate" are handled. A pick is then *right*, *under*, or *over*;
   propose a cost weighting (under-thinks weighted more, e.g. 2:1). Write the
   rule in the report so `classifier/score-candidates`,
   `classifier/evaluate-jev`, and `decide-classifier-approach` can cite it.
3. **LLM judge (optional candidate):** a non-Claude model judges the same
   pairs; report agreement with the owner, especially on "cheaper was good
   enough".
4. **Run it:** a minimal script under `scripts/` that runs the levels and
   records results; the owner judges. Log per task: task text, task type,
   model, level, tokens/cost/latency per level, each pair verdict, judge, note.
5. **Output:** the labelled set (`data/ground-truth.jsonl`, gitignored) and
   a report at `docs/research/head-to-head.md`: how often each level was the
   right one, with confidence intervals (at n≈30 a proportion is ±15–20 pp),
   the cost of higher levels, LLM-judge agreement, the scoring rule, and
   whether the problem looks worth solving (if one level is almost always
   right, a classifier adds little).

Owner touchpoints, each via `coga block --task
classifier/head-to-head-ground-truth --reason "..."`: (a) budget and API
keys, with a cost estimate (every task runs three times, plus judge calls);
(b) the task list; (c) the judging pass. The owner reviews and finishes in
the `human-owns-and-finishes` step.

## Context

- **Scope vs vision:** the vision puts "automated benchmarking of tasks
  across levels" out of v1. This ticket is the owner-approved exception
  (2026-10-08): a small manual comparison is the only way to get ground
  truth. Keep it a script; don't build a reusable harness.
- **Bandit framing:** the owner's idea: run variants, let a judge pick.
  Adjacent pairs keep each judgement a simple binary.
- **Neutrality:** runs use a Claude model, so the LLM judge should be from
  another family.
- **Feedback bias:** users report "too shallow" more readily than "less
  would have worked". Blind, randomised pairs counter it here.
- **Effort scale:** level names/count are undecided, owned by
  `_hold/decide-effort-scale-and-provider-mapping`. Use the provider's native
  low < medium < high as a placeholder ordinal scale and say so.
- **Data:** the owner approved (2026-10-08) sending real tasks to Jev and to
  a non-Claude judge. Files containing real task text stay out of git (put
  them under a gitignored `data/` and add the ignore rule). Commit only
  reports with aggregate results and redacted examples.
- Read `docs/research/state-of-the-art.md` first if it exists: prior work
  may have a judging protocol worth copying.
- **Order:** `classifier/state-of-the-art-review` →
  `classifier/head-to-head-ground-truth` (Jev desk checks may run in
  parallel) → `classifier/score-candidates` and the Jev trial →
  `decide-classifier-approach`. The owner may cancel any ticket an earlier
  result makes unnecessary (`coga mark canceled --message`).

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
