# Changelog

All notable changes to this specification will be documented here.

## v1-draft — 2026-04-26

Initial draft of agentagencies/v1.

- Adds `AGENCY.md` (top-level): runtime contract with heartbeat, authority matrix, approvals, resource ceilings.
- Inherits `COMPANY.md`, `TEAM.md`, `AGENTS.md`, `PROJECT.md`, `TASK.md`, `SKILL.md` from agentcompanies/v1.
- Clarifies canonical layout: tasks flat at `tasks/<slug>/`, skills at `skills/<slug>/`.
- Documents `tasks[] ↔ project` symmetry for project-task linking.
- Adds `metadata.dependsOn` for task dependencies within a project.
- Adds `schedule.recurrence` (RRULE / RFC 5545) for recurring projects and tasks.

Reference implementation: [openagentik/companies/paperasse](https://github.com/openagentik/companies/tree/main/paperasse).
