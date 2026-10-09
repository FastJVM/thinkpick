---
title: Commit the pending coga.toml Slack edit that blocks the recurring s...
status: done
owner: nicktoper
agent: claude
workflow:
  name: code/with-self-review
  steps:
  - name: implement
    skills:
    - code/implement
    assignee: agent
    requires: branch
  - name: self-qa
    skills:
    - code/self-qa
    assignee: agent
  - name: pr
    skills:
    - code/open-pr
    assignee: agent
    requires: pr
  - name: review
    skills:
    - code/address-pr-comments
    assignee: owner
---

## Description

## What broke

The 2026-10-08 10:32 recurring sweep launched nothing useful. Seven templates were due. `recurring/branch-sweep` launched first, and right after it the sweep stopped with a retained-state refusal (exit 75). The other six were never launched: `resolve-conflicts`, `skill-update`, `address-pr-comments`, `autoclose-merged`, `blocker-reminders` and `dream`. Four of the seven were already 3 days overdue.

## Evidence

- Run record: "a retained-state refusal (exit 75) stopped the sweep at recurring/branch-sweep" and "6 of 7 due task(s) never launched after recurring/branch-sweep".
- The branch-sweep script itself succeeded. `coga/log.md` shows `launched as a script (ticket.py)`, then `task done` and `script exited with code 0`, and the period ticket is `status: done`. The record's line saying no launch outcome was recorded for branch-sweep comes from the stop, not from the script failing.
- The last commit is `57f8022 Sync coga state before checkout return`, which touches only `coga/log.md`. That is the pre-return sync in `CheckoutBoundary.settle` (`coga/commands/launch.py`, around lines 2539–2565 of the installed package). After that sync, `git.prepare_control_checkout` did not return `prepared`/`exempt`. That set `state_sweep_withheld`, and `launch_recurring_period` (`launch.py` around line 351) turned it into `SystemExit(75)`. That exit stops the whole sweep (`recurring_runner.py` around line 2103).
- `git status` on `main` shows one non-routine change that the sync does not publish: `M coga/coga.toml`. It is a hand edit enabling Slack notifications:
  ```toml
  [notification]
  channels = ["slack"]

  [notification.slack]
  webhook = "env:SLACK_WEBHOOK_URL"
  important_webhook = "env:COGA_IMPORTANT_WEBHOOK_URL"
  ```
  This edit is preserved local dirt that makes the checkout-return check refuse. Every sweep that runs a task before this is fixed will stop the same way after its first launch.

## Where it lives

- `coga/coga.toml`, the uncommitted change in this repo. This is the direct cause.
- Behavior in the coga package (`commands/launch.py` `CheckoutBoundary.settle`, `git.prepare_control_checkout`). The behavior is intended, so no package change is needed here.

## What a fix has to do

1. Settle the `coga/coga.toml` change on `main`. Either commit it, which is safe because it only references secrets through `env:` names, or move it into the gitignored `coga/coga.local.toml` if it is meant to be machine-local. Then confirm `git status` is clean apart from routine coga state.
2. Check that `SLACK_WEBHOOK_URL` and `COGA_IMPORTANT_WEBHOOK_URL` are set wherever `coga recurring` runs, or notifications will fail next.
3. Run `coga recurring` again and confirm that the six skipped periods (2026-10-08 / 2026-W41) launch and finish, and that the sweep does not stop with exit 75.
4. Optionally add a note in the repo's recurring docs: hand edits to tracked `coga/` config must be committed before a sweep, or the first checkout return will stop the whole run.

---

Written by the `coga recurring` autofix loop from the sweep this
ticket's `run-log.md` records. The finding is an agent's
reading of that run, not a verified diagnosis: confirm it against
`run-log.md` before changing anything, and close the ticket
through the workflow's already-satisfied path if the problem was
transient or already fixed.

## Context

<!-- coga:blackboard -->

The blackboard is a notepad to be written to often as the human and agent works through a task.
