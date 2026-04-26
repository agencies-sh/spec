# Relationship with agentcompanies/v1

## TL;DR

**agentagencies/v1 is a strict superset of agentcompanies/v1.**

- Every valid agentcompanies/v1 package is a valid agentagencies/v1 package.
- agentagencies/v1 does not modify any existing manifest types.
- `SKILL.md` is owned upstream and must not be redefined (we honor that rule).

## What's added

| New | Purpose |
|---|---|
| `AGENCY.md` (top-level) | Runtime contract — heartbeat, authority matrix, approvals, resource ceilings |
| `metadata.authority` (in AGENTS.md) | Per-agent overrides of the agency-level authority defaults |
| `metadata.dependsOn` (in TASK.md) | Explicit task dependencies within a project |
| `schedule.recurrence` (in PROJECT/TASK) | RRULE-based recurring schedules |
| `tasks: [...]` ↔ `project: <slug>` symmetry | Project-task linking convention (spec was silent) |

## What's clarified (not changed)

- Tasks live at `tasks/<slug>/TASK.md` — flat, not nested under projects. The upstream spec example shows `tasks/monday-review/TASK.md` and we adopt that as canonical.
- Skills live at `skills/<slug>/SKILL.md` per the upstream canonical layout.

## Compatibility matrix

| You have… | …in agentcompanies/v1 runtime | …in agentagencies/v1 runtime |
|---|---|---|
| `COMPANY.md` only | ✅ valid company | ✅ valid company |
| `COMPANY.md` + `AGENCY.md` | ✅ AGENCY.md ignored | ✅ runs autonomously per AGENCY.md |
| `AGENCY.md` only | ❌ invalid | ✅ valid (COMPANY recommended for identity) |

## Migration

If you have an existing agentcompanies/v1 package:

1. Add a top-level `AGENCY.md` with at minimum `schema`, `kind`, `slug`, `name`, `description`, `runtime`, `authority.default: ask`. The `ask` default keeps the package safe — no actions auto-execute.
2. Optionally promote per-task `dependsOn`, recurrence rules, and `tasks: [...]` ↔ `project` symmetry.
3. Run an agentagencies/v1 validator to confirm.

Result: the package now runs autonomously on agentagencies/v1 runtimes, and remains 100% valid for agentcompanies/v1 runtimes (which simply ignore `AGENCY.md`).

## Upstream coordination

We track upstream agentcompanies/v1 evolution. When upstream introduces fields that overlap with our extensions, we will:

1. Adopt the upstream field name (deprecate ours after a 6-month grace period).
2. Document the migration in [`CHANGELOG.md`](../CHANGELOG.md).
3. Update reference implementations in [openagentik/companies](https://github.com/openagentik/companies).

We do not fork upstream. We extend it.
