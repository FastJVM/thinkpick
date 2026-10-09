---
title: Head-to-head ground truth
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

Produce the ground truth thinkpick is missing, by running real tasks
head-to-head at two effort levels and judging whether the cheaper level was
good enough. Without this we can't score any classifier (ours, Jev, or a
published one): the "right" level is the lowest one that would have
sufficed, and nobody observes it unless they compare.

Scope it as a small, mostly manual study, **not** an automated benchmarking
harness:

1. **Protocol:** ~20–30 real tasks from the owner's work (coding and
   non-coding). For each, run the same model at two adjacent levels
   concurrently, show both answers blind and in random order, and record a
   judgement: *cheaper was good enough / higher was needed / both
   inadequate*. Define what "good enough" means per task type (e.g. coding:
   tests pass / diff acceptable).
2. **Judge:** the owner is the primary judge. Optionally an LLM judge as a
   candidate: run it on the same pairs and report its agreement with the
   owner. Don't trust it for "less would have sufficed" unless agreement is
   high.
3. **Run it:** a minimal script (under `scripts/`) that runs both levels and
   records results; the owner does the judging. Log per task: task text,
   task type, model, the two levels, tokens/cost/latency for each, the
   verdict, the judge, and a free-text note.
4. **Output:** the labelled task set (`data/ground-truth.jsonl` or similar)
   plus a report at `docs/research/head-to-head.md`: what fraction of tasks
   needed the higher level, how much the higher level cost, LLM-judge
   agreement, and what this says about whether the problem is worth solving
   (if the cheaper level is almost always fine, or almost never, a
   classifier adds little).

Call out cost before running: every task runs twice. Ask the owner for the
budget and API key. The owner reviews and finishes in the
`human-owns-and-finishes` step.

## Context

- **Scope vs vision:** `product/vision` lists "automated benchmarking of
  tasks across levels" as out of v1. This ticket is the owner-approved
  exception (2026-10-08): a small manual comparison is the only way to get
  ground truth. Keep it small and scripted; don't build a reusable harness.
- **Bandit framing:** the owner's idea is like a bandit, i.e. run two variants
  and let a judge pick. Comparing two adjacent levels per task keeps each
  judgement a simple binary.
- **Neutrality:** if the runs use Claude models, a Claude-family judge may be
  biased; prefer a different-family LLM judge, or the owner.
- **Effort scale:** level names/count are undecided (future scale ticket).
  Use the target provider's native effort knob (e.g. low/medium/high) and say
  so; the mapping to a common scale comes later.
- **Feedback bias:** users report "too shallow" more readily than "less
  would have worked". Blind, randomised pairs are what counter it here.
- Read `docs/research/state-of-the-art.md` first if it exists: prior work
  may have a judging protocol worth copying.
- The labelled set is the yardstick for `classifier/evaluate-jev` and the
  input to `decide-classifier-approach`.

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
