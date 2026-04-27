# TASK.md (v1)

**Inherited from agentcompanies/v1.** Agencies add:

- `metadata.dependsOn: [<task-slug>]` — explicit task dependencies within the same project.
- `schedule.recurrence: <RRULE>` — for recurring tasks (e.g., daily heartbeat, weekly digest).
- `schedule.dueRule: <RRULE>` — for recurring deadlines (e.g., "due 24th of each month").
- `metadata.inputs / outputs` — documented file artifacts produced/consumed by the task.
- `metadata.approval_timeout` — per-task override of the agency-level approval timeout.

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
  approval_timeout: PT2H           # optional, overrides AGENCY-level default
---
```

## Layout

Tasks live in `tasks/<slug>/TASK.md` at the agency root — **flat**, not nested under `projects/`. The `project: <slug>` field handles the relationship.

This is the canonical layout. Earlier nested layouts (`projects/<project>/tasks/<task>/TASK.md`) are deprecated and validators will warn.

---

## Recurrence semantics

When a task has `schedule.recurrence`, the runtime treats the TASK.md as a **template** and instantiates one **occurrence** per RRULE firing. Each occurrence is a separate run with its own audit record, its own approval requests, its own credits consumption.

### Worked example — monthly VAT cycle

```yaml
---
schema: agentagencies/v1
kind: task
slug: declaration-ca3
name: Monthly CA3 (VAT) declaration
assignee: comptable
project: tva-mensuelle
schedule:
  timezone: Europe/Paris
  startsAt: 2027-02-01T09:00:00+01:00
  recurrence: "FREQ=MONTHLY;BYMONTHDAY=15"     # template fires the 15th of each month
  dueRule: "FREQ=MONTHLY;BYMONTHDAY=24"        # filing deadline the 24th
metadata:
  outputs:
    - tva/ca3-{YYYY-MM}.pdf
  references:
    - skills/comptable/references/tva.md
---
```

What the runtime does:

| Time | Event |
|---|---|
| 2027-02-15 09:00 Paris | First occurrence fires. Runtime creates `tva-2027-02` task instance, assigns to `comptable`, sets `dueAt: 2027-02-24T18:00 Paris` from `dueRule`. |
| 2027-02-15 → 2027-02-24 | `comptable` works the task; outputs written to `tva/ca3-2027-02.pdf`. |
| 2027-02-24 | Deadline. If incomplete, audit log records `MISSED_DEADLINE` and the next heartbeat surfaces the blocker. |
| 2027-03-15 09:00 Paris | Second occurrence fires (independent of the first), creates `tva-2027-03`. |

`startsAt` is the **first occurrence** time (or "do not start before"). `recurrence` defines the cadence after that. `dueAt` is for non-recurring tasks; `dueRule` is for recurring ones.

### Resolving template variables in outputs

`{YYYY-MM}` (or `{YYYY}`, `{MM}`, `{DD}`, `{week}`) in `outputs` are resolved per occurrence using the firing time. Runtimes MUST honor at least these tokens; additional tokens are runtime-defined.

| Token | Example value (firing 2027-02-15) |
|---|---|
| `{YYYY}` | `2027` |
| `{MM}` | `02` |
| `{DD}` | `15` |
| `{YYYY-MM}` | `2027-02` |
| `{week}` | `2027-W07` |

---

## dependsOn semantics

`metadata.dependsOn: [<task-slug>]` declares that this task cannot begin until each named task has reached a terminal state (`succeeded`, `failed`, or `skipped`).

### Cross-occurrence dependencies

For recurring tasks, `dependsOn` resolves **per occurrence**: occurrence N of a task depends on occurrence N of each listed task — not the entire history.

Example: a monthly project with three tasks, all recurring on the 15th:

```yaml
# preparation/TASK.md
schedule:
  recurrence: "FREQ=MONTHLY;BYMONTHDAY=15"

# review/TASK.md
schedule:
  recurrence: "FREQ=MONTHLY;BYMONTHDAY=15"
metadata:
  dependsOn: [preparation]

# ship/TASK.md
schedule:
  recurrence: "FREQ=MONTHLY;BYMONTHDAY=15"
metadata:
  dependsOn: [review]
```

In month N, `ship` waits for `review-N`, which waits for `preparation-N`. Month N+1 is fully independent.

### Cross-project dependencies

`dependsOn` slugs MUST refer to tasks within the **same project**. To express a dependency across projects, the spec doesn't yet provide a primitive — runtimes typically model this through the manager agent receiving notifications.

---

## Approval timeout overrides

`metadata.approval_timeout` (ISO 8601 duration) overrides `AGENCY.md > approvals.default_timeout` for this task only. The most-restrictive value wins.

```yaml
# tasks/urgent-vat-deadline/TASK.md
metadata:
  approval_timeout: PT2H   # 2 hours — agency-level may be P3D
```

Use this for time-sensitive tasks where a 3-day default is too long (regulatory deadlines, customer-facing SLAs). Also useful in the other direction — a long-running research task can be `P14D` to avoid premature timeout-denials.
