# TASK.md (v1)

**Inherited from agentcompanies/v1.** Agencies add:

- `metadata.dependsOn: [<task-slug>]` — explicit task dependencies within the same project.
- `schedule.recurrence: <RRULE>` — for recurring tasks (e.g., daily heartbeat, weekly digest).
- `schedule.dueRule: <RRULE>` — for recurring deadlines (e.g., "due 24th of each month").
- `metadata.inputs / outputs` — documented file artifacts produced/consumed by the task.

## Field reference

```yaml
---
schema: agentagencies/v1
kind: task
slug: my-task
name: ...
description: ...
assignee: <agent-slug>
project: <project-slug>            # symmetric with the project's `tasks[]`
schedule:
  timezone: Europe/Paris
  startsAt: 2027-01-15T09:00:00+01:00
  dueAt: 2027-01-31T18:00:00+01:00
  recurrence: "FREQ=MONTHLY;BYMONTHDAY=15"
  dueRule: "FREQ=MONTHLY;BYMONTHDAY=24"
metadata:
  dependsOn: [other-task-slug]
  inputs: [data/input.json]
  outputs: [reports/output.pdf]
  references: [skills/<slug>/references/<doc>.md]
---
```

## Layout

Tasks live in `tasks/<slug>/TASK.md` at the agency root — **flat**, not nested under `projects/`. The `project: <slug>` field handles the relationship.

This is the canonical layout. Earlier nested layouts (`projects/<project>/tasks/<task>/TASK.md`) are deprecated and validators will warn.
