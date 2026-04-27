---
schema: agentagencies/v1
kind: task
slug: greet
name: Propose a greeting
description: |
  Planner drafts a brief: who's the audience today, what tone, what length.
assignee: planner
project: hello-cycle
tags:
  - planning
schedule:
  timezone: Europe/Paris
  recurrence: "FREQ=DAILY;BYHOUR=9"
metadata:
  outputs:
    - briefs/hello-{YYYY-MM-DD}.md
  references:
    - skills/greeting/SKILL.md
---

# Propose a greeting

Planner produces a brief — audience, tone, length, any specific theme.
Output goes to `briefs/hello-{YYYY-MM-DD}.md`. Operator approves.

The `respond` task waits on this via `dependsOn: [greet]`.
