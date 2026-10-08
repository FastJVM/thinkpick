---
title: coga-build
status: in_progress
owner: nicktoper
agent: claude
workflow:
  name: build/onboarding
  steps:
  - name: gather-and-spec
    skills: []
    assignee: agent
  - name: generate-batch
    skills: []
    assignee: agent
step: 2 (generate-batch)
---

## Description

First-run onboarding. One scripted question — "What do you want to build?" —
then an agent-led chat draws out the rest, ending in a short vision you sign off
on and a flat batch of draft tickets you can immediately `coga launch`. Empty
repos only; no scan. Launching this ticket starts the chat.

## Context

Empty until the `gather-and-spec` step runs at first launch — the agreed vision
is written to `product/vision/SKILL.md` under the configured contexts directory
(`coga/contexts/` by default), and raw intake notes stay on the blackboard.

<!-- coga:blackboard -->

The blackboard is a notepad for the human and agent to use while working
through this task. For onboarding it holds the raw intake from the
gather-and-spec chat — the working notes behind the vision, which stay here
rather than in the durable `contexts/product/vision` doc.

## gather-and-spec intake (2026-09-22)

- Ask: "a tool to know what level of thinking of model to use in general for
  my tasks".
- Audience: the user + other interested people. Small open-source tool.
  Explicitly **not** a web app.
- Decide mode: user asked for a recommendation; agent proposed "recommend on
  the spot + log was-it-right feedback; learning deferred to v2". Tradeoff
  noted: early picks only as good as initial heuristics/cheap-model; full
  benchmarking is higher-quality data but slow/expensive. User accepted
  (didn't push back).
- Form factor: CLI + library core (user confirmed "not a webapp").
- Vision signed off in chat; written to coga/contexts/product/vision/SKILL.md.
- Deferred decisions → ticket candidates for generate-batch: classifier
  approach; provider/model support + level mapping; scale shape (named vs
  token budget); integrations (Claude Code hook); v2 learning design;
  packaging/distribution/license.

