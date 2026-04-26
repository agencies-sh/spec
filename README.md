# agentagencies/v1

> An open spec for portable AI agency packages. Extends [agentcompanies/v1](https://agentcompanies.io/specification).

[![License: CC BY 4.0](https://licensebuttons.net/l/by/4.0/88x31.png)](LICENSE)
[![Status: draft](https://img.shields.io/badge/status-v1--draft-orange.svg)](#status)

**agentagencies/v1** defines a filesystem-first manifest format for AI agencies — multi-agent organizations that take goals, delegate to specialists, and ship work under contractual approval boundaries.

It's a **strict superset** of agentcompanies/v1: any agentcompanies/v1 package is a valid agentagencies/v1 package. The extension adds:

1. **`AGENCY.md`** — a new top-level manifest expressing autonomous-operation contracts
2. **Approval contracts** — explicit authority matrices for what agents can do without asking
3. **Filesystem-first orchestration** — agencies are runnable from disk by any compliant runtime

If you have an existing agentcompanies/v1 package, you don't need to migrate. Drop in an `AGENCY.md` to opt into the agency layer.

---

## What's here

| File | Purpose |
|---|---|
| [`v1/00-overview.md`](v1/00-overview.md) | The TL;DR + design principles |
| [`v1/01-AGENCY.md`](v1/01-AGENCY.md) | The new top-level manifest — autonomy + approvals |
| [`v1/02-COMPANY.md`](v1/02-COMPANY.md) | Inherited from agentcompanies/v1 (link only) |
| [`v1/03-TEAM.md`](v1/03-TEAM.md) | Inherited |
| [`v1/04-AGENTS.md`](v1/04-AGENTS.md) | Inherited |
| [`v1/05-PROJECT.md`](v1/05-PROJECT.md) | Inherited + agency clarifications |
| [`v1/06-TASK.md`](v1/06-TASK.md) | Inherited + RRULE recurrence + dependsOn |
| [`v1/07-SKILL.md`](v1/07-SKILL.md) | **Must not be redefined** (per agentcompanies/v1) |
| [`v1/relationship-with-agentcompanies-v1.md`](v1/relationship-with-agentcompanies-v1.md) | Compat + interop notes |
| [`v1/examples/`](v1/examples/) | Reference implementations |

## Status

**v1-draft.** API/field names may change before v1.0. Reference implementations: [openagentik/companies](https://github.com/openagentik/companies).

## License

This specification is licensed under [Creative Commons Attribution 4.0 International](LICENSE) (CC-BY-4.0). Implementations are encouraged to use whatever license fits their project.

## Contributing

Discussion happens in [issues](https://github.com/agencies-sh/spec/issues). Substantive changes require an issue + RFC PR.

---

*Stewarded by [openagentik](https://github.com/openagentik). Vendor-neutral — no one owns the spec.*
