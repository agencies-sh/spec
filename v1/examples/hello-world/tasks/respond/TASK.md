---
schema: agentagencies/v1
kind: task
slug: respond
name: Write the greeting
description: |
  Doer reads the approved brief and writes the actual greeting.
assignee: doer
project: hello-cycle
tags:
  - execution
schedule:
  timezone: Europe/Paris
  recurrence: "FREQ=DAILY;BYHOUR=9"
metadata:
  dependsOn:
    - greet
  inputs:
    - briefs/hello-{YYYY-MM-DD}.md
  outputs:
    - outputs/hello-{YYYY-MM-DD}.md
  references:
    - skills/greeting/SKILL.md
---

# Write the greeting

Reads `briefs/hello-{YYYY-MM-DD}.md` (approved by the operator after the
`greet` task), writes `outputs/hello-{YYYY-MM-DD}.md`. Operator approves.

`dependsOn: [greet]` means this task cannot start until the matching
`greet` occurrence reaches a terminal state. See [TASK.md spec — Cross-occurrence dependencies](../../06-TASK.md#cross-occurrence-dependencies).
