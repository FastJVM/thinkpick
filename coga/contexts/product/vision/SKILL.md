---
name: product/vision
description: Starter vision for thinkpick — what it is, who it's for, what success looks like, and the PoC scope shape. A living doc; the owner edits it as the project evolves.
---

# thinkpick — vision

*Living starter doc. Edit as the project evolves; this is a direction, not a
finished spec.*

**What.** thinkpick is a small open-source tool that tells you how much
thinking / reasoning effort to give a model for a task. You describe the task;
it returns a recommended level with a short reason.

**Who.** The author first, then any developer working with reasoning models
who is tired of guessing the effort level — overpaying on easy tasks or
getting shallow answers on hard ones.

**Success.** People trust the pick enough to use it by default: it rarely
under-thinks hard tasks, and it saves cost and latency on easy ones.

## Stage: proof of concept

First question: **is this even possible?** Can the right effort level be
predicted from a task description, and does the prediction improve with
per-project/user corrections? Keep everything as simple as possible until
that is answered.

## v1 scope (PoC)

- Task description in → recommended level + one-line reason out.
- Runs locally as a small CLI with a library core (a Python script is fine).
  Not a web app, not a hosted service. A Claude Code hook is a likely next
  step, not the first one.
- An API key is acceptable; offline use is not required for the PoC.
- **Learns from day one:** "too little / right / too much" feedback is logged
  locally and fed back into later picks, per project/user.

**Out of v1:** automated benchmarking of tasks across levels, hosted service
or web UI. (Exception: a small, mostly manual head-to-head comparison to get
ground truth is in scope; see `classifier/head-to-head-ground-truth`.)

## Open decisions

Deliberately deferred at onboarding; each is a "decide/evaluate" ticket:

- Classifier approach: evidence first (`classifier/*` tickets: state of the
  art, Jev, head-to-head ground truth), then build-or-buy in
  `decide-classifier-approach`. Cheap LLM call + logged corrections is the
  leading build candidate.
- Which providers/models to support, and how their effort/thinking knobs map
  onto one common scale.
- Shape of the scale itself: named levels vs token budgets.
- Integrations beyond the CLI (e.g. a Claude Code hook/plugin).
- How logged feedback feeds learning beyond in-prompt examples.
- Packaging, distribution, and license.
