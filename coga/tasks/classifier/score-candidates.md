---
title: Score candidates
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

Score the build candidates on the head-to-head labelled set, so the final
decision compares measured results rather than reasoning. thinkpick is a
small CLI that recommends a thinking/effort level for a task; until now only
Jev was going to be measured, which would leave the leading build candidate
judged by argument alone, the mistake the owner already rejected.

Score, with the shared scoring rule:

1. **Baseline:** always medium.
2. **Simple heuristic:** a few rules on the task text (length, keywords,
   task type); keep it deliberately small.
3. **Cheap LLM call:** a short prompt asking a cheap model for level +
   confidence + one-line reason. Run it **without examples**, then **with
   earlier labelled tasks as in-prompt examples** (e.g. leave-one-out or
   chronological: the first k tasks as examples for the rest), to estimate
   how much "learning from corrections" helps.

Done means scripts under `scripts/` (throwaway, not product code) and a
report at `docs/research/candidate-scores.md`: per candidate, right / under
/ over rates, weighted cost, comparison with the baseline (with intervals;
n≈30 is a PoC signal, not proof), the examples-vs-no-examples difference,
and per-pick cost and latency. Include Jev's numbers from
`docs/research/jev-evaluation.md` in the comparison table if available.

Owner touchpoint: `coga block --task classifier/score-candidates --reason
"..."` for an API key if one isn't already configured. The owner reviews and
finishes in the `human-owns-and-finishes` step.

## Context

- Needs `data/ground-truth.jsonl` and the scoring rule from
  `classifier/head-to-head-ground-truth`; if they don't exist, block.
- **Overfitting:** with ~30 tasks, don't tune the heuristic or prompt on the
  same tasks you score. Hold some out, or say plainly that you tuned on
  them.
- **Feedback bias:** in real use, corrections will skew toward "too little".
  The labelled set is cleaner than live feedback, so note that the
  with-examples gain may be optimistic.
- **Effort scale:** level names/count are undecided, owned by
  `_hold/decide-effort-scale-and-provider-mapping`. Use the provider's native
  low < medium < high as a placeholder ordinal scale and say so.
- **Data:** the owner approved (2026-10-08) sending real tasks to Jev and to
  a non-Claude judge. Files containing real task text stay out of git (put
  them under a gitignored `data/` and add the ignore rule). Commit only
  reports with aggregate results and redacted examples.
- **Order:** `classifier/state-of-the-art-review` →
  `classifier/head-to-head-ground-truth` (Jev desk checks may run in
  parallel) → `classifier/score-candidates` and the Jev trial →
  `decide-classifier-approach`. The owner may cancel any ticket an earlier
  result makes unnecessary (`coga mark canceled --message`).

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
