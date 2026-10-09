---
title: Local was-it-right feedback log
status: draft
owner: nicktoper
contexts:
  - product/vision
workflow:
  name: code/design-then-implement
  steps:
  - name: design
    skills:
    - code/design
    assignee: agent
  - name: evaluate-design
    skills:
    - code/review-design
    assignee: other-agent
  - name: review-design
    skills: []
    assignee: owner
  - name: implement
    skills:
    - code/implement
    assignee: agent
    requires: branch
  - name: open-pr
    skills:
    - code/open-pr
    assignee: agent
    requires: pr
  - name: review
    skills:
    - code/address-pr-comments
    assignee: owner
step: 1 (design)
---

## Description

Optional *too little / right / too much* feedback after a pick, logged
locally per project/user together with the level actually used, and fed back
into later picks: thinkpick learns from day one (see `product/vision`). How
the log feeds the classifier (e.g. recent corrections as in-prompt examples)
is set by `decide-classifier-approach`; the log fields proposed there are a
starting point, not a binding schema.

## Context

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
