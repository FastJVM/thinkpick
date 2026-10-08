# thinkpick

A small open-source tool that tells you how much thinking / reasoning effort
to give a model for a task. Describe the task; get a recommended level with a
short reason. See [the vision](coga/contexts/product/vision/SKILL.md).

## Status

Proof of concept. Nothing is built yet. The open question comes first: **can
the right effort level be predicted from a task description, and does the
prediction improve with per-project corrections?** That question is the one open
ticket (a draft),
[`decide-classifier-approach`](coga/tasks/decide-classifier-approach.md).

The build tickets are written but **on hold** until it is answered, in
[`coga/tasks/_hold/`](coga/tasks/_hold/); coga skips `_`-prefixed folders, so
`coga status` does not list them. To start one, move it into `coga/tasks/`.

## Working on it

This repo uses [coga](https://github.com/FastJVM/coga) for tasks and context.
In a fresh clone, first give coga your name (the file is gitignored):

```sh
echo 'user = "<your-name>"' > coga/coga.local.toml
coga validate
coga launch <slug>
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
