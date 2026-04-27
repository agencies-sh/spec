# Changelog

All notable changes to this specification will be documented here.

## v1-draft — 2026-04-27 (revision 2)

Thicker spec body. No field changes — content additions only.

- `01-AGENCY.md`: added verb resolution order (per-agent → agent-local → agency default → spec default), worked authority-matrix example for a 3-agent content team, heartbeat tick walkthrough with common RRULE patterns, full approval lifecycle diagram, per-task timeout overrides, resource-ceiling enforcement semantics, minimum audit record shape.
- `06-TASK.md`: added recurrence semantics with monthly-VAT worked example, output template-variable token table (`{YYYY}`, `{YYYY-MM}`, `{week}`, etc.), cross-occurrence `dependsOn` semantics with three-task monthly cycle example, `metadata.approval_timeout` override field.
- `examples/hello-world/`: added smallest complete agency exercising all 7 manifest types. Validates clean against [openagentik/companies validator](https://github.com/openagentik/companies/blob/main/scripts/validate-agentcompanies.js).
- `examples/README.md`: indexes the inline hello-world example and points at production references.

## v1-draft — 2026-04-26

Initial draft of agentagencies/v1.

- Adds `AGENCY.md` (top-level): runtime contract with heartbeat, authority matrix, approvals, resource ceilings.
- Inherits `COMPANY.md`, `TEAM.md`, `AGENTS.md`, `PROJECT.md`, `TASK.md`, `SKILL.md` from agentcompanies/v1.
- Clarifies canonical layout: tasks flat at `tasks/<slug>/`, skills at `skills/<slug>/`.
- Documents `tasks[] ↔ project` symmetry for project-task linking.
- Adds `metadata.dependsOn` for task dependencies within a project.
- Adds `schedule.recurrence` (RRULE / RFC 5545) for recurring projects and tasks.

Reference implementation: [openagentik/companies/paperasse](https://github.com/openagentik/companies/tree/main/paperasse).
