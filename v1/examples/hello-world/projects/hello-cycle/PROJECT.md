---
schema: agentagencies/v1
kind: project
slug: hello-cycle
name: Daily hello cycle
description: |
  Recurring daily greeting. Planner proposes, doer writes. The simplest
  end-to-end project demonstrating tasks[]/project symmetry and
  dependsOn.
version: 0.1.0
tags:
  - demo
metadata:
  team: home
  schedule:
    timezone: Europe/Paris
    startsAt: 2027-01-01T09:00:00+01:00
    recurrence: "FREQ=DAILY;BYHOUR=9"
tasks:
  - greet
  - respond
---

# Daily hello cycle

Sample recurring project. The two tasks (`greet` then `respond`) re-fire
every day at 9am Paris.

## Per-day execution

| Time | Event |
|---|---|
| 09:00 | Heartbeat fires. `greet` task instance starts (planner). |
| ~09:05 | `greet` completes. Approval requested for `brief.md`. |
| ~09:10 | Operator approves brief. `respond` becomes runnable. |
| ~09:15 | `respond` starts (doer). Writes greeting. Approval requested for output. |
| ~09:20 | Operator approves. `outputs/hello-{YYYY-MM-DD}.md` finalized. |

If the operator doesn't approve within `P1D` (the agency-level timeout),
the day's tasks audit-log a `TIMEOUT_DENIED` and the next heartbeat starts
a fresh occurrence.
