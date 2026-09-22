---
name: verify
description: "Verify web app changes by using them end to end with agent-browser."
disable-model-invocation: true
---

Drive the changed flow with agent-browser before you call the work done. Open the running app. Take an interactive snapshot. Then click, type, submit, and navigate the path a user takes through the change.

Read the installed agent-browser skill for the CLI. Snapshot refs die after navigation or a DOM change. Snapshot again before the next click or fill.

Open every screen that shares the state, data, or components you touched. Cover the empty list, the error, and the flag that is off. If the path breaks, fix it and drive it again. Stop only when the result holds in the UI.
