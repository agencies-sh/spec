# hello-world

The smallest complete agentagencies/v1 agency. Demonstrates every manifest type.

## Layout

```
hello-world/
├── COMPANY.md                          ← identity
├── AGENCY.md                           ← runtime contract (heartbeat, authority, approvals)
├── README.md                           ← this file
├── teams/home/TEAM.md                  ← 1 team, manager: planner
├── agents/
│   ├── planner/AGENTS.md               ← proposes work
│   └── doer/AGENTS.md                  ← executes
├── projects/hello-cycle/PROJECT.md     ← daily recurring project
├── tasks/
│   ├── greet/TASK.md                   ← planner produces brief
│   └── respond/TASK.md                 ← doer writes greeting (dependsOn: greet)
└── skills/
    └── greeting/SKILL.md               ← placeholder SKILL package
```

## What this demonstrates

| Concept | Where |
|---|---|
| `AGENCY.md` runtime contract | [AGENCY.md](AGENCY.md) — heartbeat, authority matrix, approvals, ceilings |
| Per-agent authority overrides | [AGENCY.md](AGENCY.md) — `authority.by_agent.planner` vs `doer` |
| Daily heartbeat | [AGENCY.md](AGENCY.md) — `runtime.heartbeat: "FREQ=DAILY;BYHOUR=9"` |
| Tasks ↔ project symmetry | [PROJECT.md](projects/hello-cycle/PROJECT.md) `tasks: [greet, respond]` + each task's `project: hello-cycle` |
| Recurrence (RRULE) | [TASK.md](tasks/greet/TASK.md) — `schedule.recurrence` |
| Cross-occurrence dependsOn | [TASK.md respond](tasks/respond/TASK.md) — depends on the same-day occurrence of `greet` |
| Template variables in outputs | `outputs/hello-{YYYY-MM-DD}.md` resolved per occurrence |
| SKILL reference (not redefinition) | [agent → SKILL link](agents/planner/AGENTS.md) |

## Validate

Use the validator from [openagentik/companies](https://github.com/openagentik/companies/blob/main/scripts/validate-agentcompanies.js):

```bash
git clone https://github.com/openagentik/companies /tmp/oa-companies
cp -R . /tmp/oa-companies/hello-world
cd /tmp/oa-companies && node scripts/validate-agentcompanies.js hello-world
# → 1 company, 1 team, 2 agent, 1 project, 2 task, 1 skill
# → ✓ all references resolve
```

## Daily walkthrough

| Time | Event |
|---|---|
| 09:00 Paris | Heartbeat fires. `greet` task instance for today starts (planner). |
| ~09:05 | Planner outputs `briefs/hello-2027-01-01.md`. Approval requested via `in_app`. |
| ~09:10 | Operator approves. `respond` task becomes runnable. |
| ~09:15 | Doer reads brief, writes `outputs/hello-2027-01-01.md`. Approval requested. |
| ~09:20 | Operator approves. Day complete. Next occurrence: 09:00 tomorrow. |
| 09:00 + 24h | If anything was unapproved at the timeout, audit logs `TIMEOUT_DENIED`. Today's instances close. New occurrences start fresh tomorrow. |

This walkthrough is normative for runtime implementations — see [AGENCY.md spec — Heartbeat](../../01-AGENCY.md#heartbeat) and [AGENCY.md spec — Timeout semantics](../../01-AGENCY.md#timeout-semantics).
