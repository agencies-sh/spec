# agentagencies/v1 — Overview

## Why a new spec on top of agentcompanies/v1?

agentcompanies/v1 (Paperclip, paperclipai/paperclip) defines **what** an AI company looks like as a portable filesystem package: org chart, roles, skills, projects, tasks. It's deliberately runtime-agnostic.

agentagencies/v1 adds the **operational contract** that turns a static company package into something a runtime can autonomously operate:

- **Authority matrix** — what each agent can do without human approval
- **Approval contracts** — what each agent must escalate, and to whom
- **Heartbeat** — when does the agency tick proactively, vs reacting to user input
- **Resource ceilings** — credit / token / time / cost ceilings per agent and per project
- **Filesystem-first runtime contract** — the spec a runtime promises to honor

A package without an `AGENCY.md` is still a valid company. With one, it's a runnable agency.

## Design principles

1. **Strict superset** — any agentcompanies/v1 package is valid. We never break upstream.
2. **Filesystem-first** — manifests are the source of truth. No required databases, no external registries.
3. **Provenance always** — every vendored asset (especially SKILLs) carries `metadata.sources` with repo + commit + license.
4. **Authority is opt-in** — by default, all actions require approval. The author of an agency explicitly grants autonomous authority per agent.
5. **Vendor-neutral** — the spec doesn't prescribe an LLM, an MCP catalog, or a billing system. Runtimes plug those in.
6. **Validators ship** — every manifest format includes a referenced zero-dep validator. Specs that can't be checked don't exist.

## Filesystem layout (canonical)

```
my-agency/
├── AGENCY.md                    ← v1 NEW: autonomy + approvals
├── COMPANY.md                   ← agentcompanies/v1
├── teams/<slug>/TEAM.md
├── agents/<slug>/AGENTS.md
├── projects/<slug>/PROJECT.md
├── tasks/<slug>/TASK.md         ← FLAT (canonical), not nested under projects/
└── skills/<slug>/SKILL.md       ← agentcompanies/v1 SKILL.md (untouched)
```

## What's intentionally NOT in v1

- A REST API. (Spec, not service.)
- A CLI. (See [openagentik/cli](https://github.com/openagentik/cli) for one reference.)
- A required runtime. (Multiple are encouraged — Paperclip, Guilde, your own.)
- Billing / metering / observability conventions. (TBD in v1.x as the ecosystem matures.)
