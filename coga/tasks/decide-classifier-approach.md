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

## Evaluator review

(Cold review of all four tickets in the set: `classifier/state-of-the-art-review`, `classifier/evaluate-jev`, `classifier/head-to-head-ground-truth`, `decide-classifier-approach`. 2026-10-08.)

Overall the set is in good shape: each ticket has a clear Description, a concrete output path and a done list, and Context copies the needed facts instead of attaching broad docs. Biggest problems are set-level: the leading build candidate (cheap LLM call) is never measured; no shared rule for scoring a 3-level pick against binary pairwise labels; two tickets need the owner mid-`agent-produces`, which the workflow doesn't model.

Sizes: no per-ticket layer over 40%; only the fixed base prompt is (~53–54%). `product/vision` is 18% of head-to-head and decide.

Repo facts checked: `docs/`, `scripts/`, `data/` don't exist yet. The "future scale ticket" exists as `coga/tasks/_hold/decide-effort-scale-and-provider-mapping.md`. Vision edit is uncommitted.

### 1. state-of-the-art-review
- Clear to start: yes. Done: mostly; "covers the relevant prior work" is unbounded — add a time box/size (≈10–20 sources, 3–6 pages, depth over breadth).
- Workflow fits; no contexts needed.
- Overlap with evaluate-jev on jcm-router — landscape-level only here.
- Say who decides to skip siblings (owner, in human-owns-and-finishes, via `coga mark canceled --message`).

### 2. evaluate-jev
- Desk checks clear; trial under-specified: the label set / instruction sent to Jev largely decides the result; no rule for scoring a low/med/high pick against binary adjacent-pair labels (e.g. pick ≤ cheaper when cheaper sufficed = correct; pick < higher when higher needed = under-think, weighted more).
- Workflow: the key request and fallback owner-rating need the owner during agent-produces — make `coga block` explicit.
- Scope: desk checks (anytime) vs trial (gated on head-to-head). Split, or gate the trial and drop the "impression" fallback, which decide may lean on anyway.
- Verify Jev/TypeSafe AI/the gist exist first; stop loudly if not. Data egress: real owner tasks go to a third-party API — ask.

### 3. head-to-head-ground-truth
- Protocol well thought out. Gaps: which provider/model (drives script and cost); which adjacent pair(s) — one pair gives ground truth for one boundary only, limiting later tickets; coding tasks need agentic runs in a repo at two settings — far more than a minimal API script. Restrict v0 to single-call tasks, or have the owner run Claude Code twice by hand and the script only record.
- Done checkable; add uncertainty reporting (±15–20 pp at n=20–30).
- Workflow: owner judging is the core and sits inside agent-produces — explicit block points (budget/key, task list, judging) or split protocol+script vs run+judge+analyse.
- `product/vision` could be dropped (needed fact already copied) — mild trim.
- Largest ticket; coding-task handling is where it could become the forbidden harness.
- Privacy: real tasks committed to `data/ground-truth.jsonl` in an open-source repo — commit, redact, or gitignore? Cross-family judge adds a second provider key. "Concurrently" is unnecessary wording.

### 4. decide-classifier-approach
- Clear; done checkable; workflow fits; keep `product/vision`. "Offline" should be weighted low for the PoC.
- Item 3 "against the head-to-head set where possible": no ticket runs heuristics, always-middle, or the cheap LLM call over the set, so the leading candidate is again judged by reasoning — the failure mode the owner rejected.
- Name the held tickets (`decide-effort-scale-and-provider-mapping`, `local-was-it-right-feedback-log`).

### Set-level
- State one ordering line in each ticket: SOTA → head-to-head ‖ jev-desk → jev-trial → decide.
- Big gap: unequal evidence. Add baseline/candidate scoring on the labelled set (always-middle, simple heuristic, cheap LLM prompt) — in head-to-head or a new `classifier/score-candidates` before decide.
- Define the pick-vs-pairwise scoring rule once (head-to-head), referenced by evaluate-jev and decide.
- Outside the set: `_hold/local-was-it-right-feedback-log` says "No learning in v1", contradicting the vision's "Learns from day one".

### Prioritized recommendations
1. Add candidate/baseline scoring on the head-to-head set.
2. Head-to-head: settle provider/model, adjacent pair(s), coding-task handling before launch.
3. Define the scoring rule once.
4. Explicit `coga block` owner touchpoints in head-to-head and evaluate-jev (or split).
5. Gate the Jev trial on head-to-head; verify Jev/vendor/gist exist first.
6. Privacy/data egress decisions.
7. Name the held tickets; fix the feedback-log contradiction.
8. SOTA: time box; owner cancels skipped siblings; jcm-router landscape-only.
9. Minor: drop vision from head-to-head; weight offline low; confidence intervals; commit the vision edit.
