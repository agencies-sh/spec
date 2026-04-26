# PROJECT.md (v1)

**Inherited from agentcompanies/v1.** Agencies clarify:

- `tasks: [<slug>]` lists tasks belonging to this project. Companion field on each task is `project: <slug>`. Both must be set; validators check symmetry.
- `metadata.schedule` MAY include `recurrence: <RRULE>` (RFC 5545) to express recurring projects (e.g., monthly VAT cycle, annual close).
- `metadata.outputs` MAY list expected artifacts. Format is free-form (paths or descriptions).

## Field reference

```yaml
---
schema: agentagencies/v1
kind: project
slug: my-project
name: ...
description: ...
tasks: [task-one, task-two]      # symmetric with each task's `project` field
metadata:
  schedule:
    timezone: Europe/Paris
    startsAt: 2027-01-01T09:00:00+01:00
    endsAt: 2027-12-31T18:00:00+01:00      # optional
    recurrence: "FREQ=MONTHLY;BYMONTHDAY=1" # optional, RFC 5545
  outputs:
    - reports/q1-summary.pdf
---
```
