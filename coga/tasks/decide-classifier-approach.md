---
title: Decide classifier approach
status: draft
owner: nicktoper
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

Choose how the pick is made: heuristics, a cheap LLM call, or a hybrid. The pick quality early on depends on this; prepare tradeoffs (quality, cost, latency, offline use) for the owner to decide.

## Context

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
