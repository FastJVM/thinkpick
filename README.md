# thinkpick

Thinkpick is a proposed small open-source tool for recommending how much
thinking / reasoning effort to give a model for a task. Describe the task;
get a level and a short reason. See [the vision](coga/contexts/product/vision/SKILL.md).

## Status

**Paused, not abandoned (owner decision, 2026-10-28).** Nothing has been
implemented or experimentally validated. The first plan assumed we could
select a good reasoning-effort classifier without first establishing what
"good" means.

A small low/medium/high API study on isolated tasks might help test that
assumption, but would not prove cost, latency, or quality gains inside real
Claude Code or Codex sessions. Measuring the practical benefit requires an
agent-workflow integration (potentially a hook, launcher, or proxy) and
end-to-end evaluation. That investment is not justified yet; existing
routers may be candidates for reuse if work resumes.

**This is a decision to defer, not evidence that effort routing fails.**
The [product vision](coga/contexts/product/vision/SKILL.md#decision-2026-10-28-paused-not-abandoned)
records the reasoning and conditions for restarting.

The four `classifier/*` research tickets and
[`decide-classifier-approach`](coga/tasks/decide-classifier-approach.md)
remain drafts, not completed research or an active project plan. Build
tickets remain under [`coga/tasks/_hold/`](coga/tasks/_hold/).

## Inspecting or revisiting the plan

This repo uses [coga](https://github.com/FastJVM/coga) for tasks and context.
In a fresh clone, first give coga your name (the file is gitignored):

```sh
echo 'user = "<your-name>"' > coga/coga.local.toml
coga validate
coga status
coga show decide-classifier-approach
```

Notifications are off in the shared `coga/coga.toml`. If your shell exports
`SLACK_WEBHOOK_URL`, coga refuses to run until you unset it or declare it in
your own `coga/coga.local.toml`:

```toml
[notification]
channels = ["slack"]

[notification.slack]
webhook = "env:SLACK_WEBHOOK_URL"
```
