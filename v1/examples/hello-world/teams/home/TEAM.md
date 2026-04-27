---
schema: agentagencies/v1
kind: team
slug: home
name: Home
description: |
  The whole team. Planner manages, doer executes.
manager: planner
tags:
  - demo
includes:
  - agents/planner/AGENTS.md
  - agents/doer/AGENTS.md
metadata:
  reporting_lines:
    doer: planner
---

# Home

Two agents, flat structure. The `manager: planner` field tells the runtime
which agent receives heartbeat ticks and assigns work for the team.
