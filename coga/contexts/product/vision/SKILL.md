---
name: product/vision
description: Thinkpick's product intent, paused decision, proposed scope, and conditions for restarting.
---

# thinkpick — vision

*Living starter doc. Edit as the project evolves; this is a direction, not a
finished spec.*

**What (proposed).** Thinkpick would be a small open-source tool that
recommends how much reasoning effort to give a model for a task. The user
describes a task; the tool returns a level and a short reason.

**Who.** The author first, then any developer working with reasoning models
who is tired of guessing the effort level — overpaying on easy tasks or
getting shallow answers on hard ones.

**Success.** People trust the pick enough to use it by default: it rarely
under-thinks hard tasks, and it saves cost and latency on easy ones.

## Decision (2026-10-28): paused, not abandoned

**Do not invest in Thinkpick's experiments or implementation for now.** This is
an owner prioritization decision, not an experimental verdict. No classifier,
benchmark, proxy, or agent integration has been built or evaluated.

The original plan assumed we could choose an approach for predicting the
lowest sufficient reasoning effort, but we have no measured ground truth.
The proposed 20–30-task low/medium/high API comparison could test that
question on isolated calls. It would **not** establish whether an effort
selector saves time or money without hurting quality in multi-step Claude Code
or Codex sessions.

Evaluating that practical benefit needs a way to choose effort within a real
agent workflow and to measure end-to-end outcomes, latency, and total cost,
including the cost of routing itself. A hook or launcher might suffice; a
proxy is another option, **not a requirement** for the narrow API study.
Existing routers/classifiers might also be reused. None has been shown in
this project to deliver a net benefit for the intended workflows, so building
a new classifier or integration now is not justified.

**Revisit only if** a low-overhead integration becomes practical, credible
external evidence suggests meaningful real-agent gains, or the owner funds a
bounded end-to-end experiment. Before work resumes, set a fixed-effort
baseline, define acceptable quality/underthinking risk, measure all routing
overhead, and decide what net cost/latency improvement would matter. Re-scope
the experiment to reflect the intended agent workflow, unless a narrower
API-only study is explicitly useful.

The research and classifier-decision tickets remain drafts, and product
implementation tickets remain under `coga/tasks/_hold/`. These preserve
the proposed plan; they are not completed findings or an active work queue.
The project can be resumed by an explicit owner decision, not simply by
treating the old tickets as ready to run.

## Original proof-of-concept question (unanswered)

The proposed first question was whether the right effort level could be
predicted from a task description and improved with per-project/user
corrections. No experiment has answered it.

## Proposed v1 scope (on hold)

- Task description in → recommended level + one-line reason out.
- Runs locally as a small CLI with a library core (a Python script is fine).
  Not a web app, not a hosted service. A Claude Code hook was a possible
  later integration, not part of the initial API-only test.
- An API key is acceptable; offline use is not required for the PoC.
- **Learns from day one:** "too little / right / too much" feedback is logged
  locally and fed back into later picks, per project/user.

**Out of the original v1 proposal:** automated benchmarking of tasks across levels, hosted service
or web UI. (Exception: a small, mostly manual head-to-head comparison to get
ground truth is in scope; see `classifier/head-to-head-ground-truth`.)

## Questions to revisit if restarted

The original plan deferred these decisions. None is settled, and the listed
tickets are not authorization to resume work:

- Classifier approach: evidence first (`classifier/*` tickets: state of the
  art, Jev, head-to-head ground truth), then build-or-buy in
  `decide-classifier-approach`. Cheap LLM call + logged corrections was a
  leading hypothesis, not a validated choice.
- Which providers/models to support, and how their effort/thinking knobs map
  onto one common scale.
- Shape of the scale itself: named levels vs token budgets.
- Integrations beyond the CLI (e.g. a Claude Code hook/plugin).
- How logged feedback feeds learning beyond in-prompt examples.
- Packaging, distribution, and license.
