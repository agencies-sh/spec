---
schema: agentagencies/v1
kind: agency
slug: hello-world
name: Hello World
description: |
  Runtime contract for the hello-world demo. Conservative defaults so the
  example matches what a real agency should ship.
version: 0.1.0
license: MIT
runtime:
  heartbeat: "FREQ=DAILY;BYHOUR=9"
  timezone: Europe/Paris
  resource_ceilings:
    credits_per_day: 1000
    credits_per_task: 100
    cost_eur_per_day: 1.00
authority:
  default: ask
  by_agent:
    planner:
      web_search: auto
      web_fetch: auto
      file_read: auto
      file_write: ask
      send_message_internal: auto
      send_message_external: ask
    doer:
      file_read: auto
      file_write: ask
      send_message_internal: auto
      send_message_external: ask
approvals:
  channel: in_app
  to: "@operator"
  default_timeout: P1D
---

# Hello World — runtime contract

Reads + research are auto. File writes ask. External messages always ask.
This is the recommended posture for a new agency until the operator builds
trust with each agent.

## How approvals flow

`default_timeout` of `P1D` (one day) means: if the operator doesn't reply
within 24 hours, the request auto-denies and the agent must surface the
blocker on the next tick.

For a real agency, `P3D` is more common — `P1D` here keeps the demo loop
short.
