# AGENCY.md (v1)

The top-level manifest that turns a static company package into a runnable agency.

## When to ship one

- **Always**, if you want any runtime to operate the package autonomously.
- **Never required** by agentcompanies/v1 — packages without `AGENCY.md` are still valid companies.

A runtime that supports agentagencies/v1 MUST honor `AGENCY.md` if present, and MAY refuse to operate a package without one.

## Frontmatter

```yaml
---
schema: agentagencies/v1
kind: agency
slug: my-agency
name: My Agency
description: |
  One paragraph: what this agency does, who it's for.

# Provenance + meta
version: 0.1.0
license: MIT
authors:
  - name: Jane Doe
tags: [demo]

# Runtime contract
runtime:
  heartbeat: "FREQ=DAILY;BYHOUR=9"     # RRULE; how often the agency self-ticks
  timezone: Europe/Paris
  resource_ceilings:
    credits_per_day: 10000
    credits_per_task: 500
    cost_eur_per_day: 5.00

# Authority matrix — what agents can do without asking
# Default: ASK (every action requires approval)
authority:
  default: ask
  by_agent:
    research-analyst:
      web_search: auto
      file_read: auto
      file_write: ask
      send_message_external: ask
    publishing-agent:
      file_read: auto
      file_write: ask
      send_message_external: ask
      publish_post: ask     # explicitly retains approval

# Approval routing
approvals:
  channel: telegram         # or "email", "slack", "in_app", "none"
  to: "@operator"
  default_timeout: P3D      # ISO 8601 duration; auto-deny after 3 days
---
```

## Body

The body of `AGENCY.md` is human-prose: who the agency serves, what it WON'T do (anti-scope), how to onboard a new operator, what's stable vs. experimental.

This is the **first thing a non-technical operator reads** when they fork the agency. Treat it as a one-page README aimed at a future you who has just cloned the repo.

## Authority verbs (initial set)

| Verb | What it covers |
|---|---|
| `web_search` | Read-only HTTP GET / search APIs |
| `web_fetch` | Read a specific URL |
| `file_read` | Read files within the agency directory |
| `file_write` | Modify / create files within the agency directory |
| `tool_call` | Invoke an MCP tool (scoped to the agent's tools list) |
| `send_message_internal` | Message another agent in the same agency |
| `send_message_external` | Message a human or external system |
| `publish_post` | Create externally-visible content (social, blog, …) |
| `transact` | Touch money / accounting / billing (always defaults to `ask`) |

Agents inherit `default` unless overridden under `by_agent.<slug>`. Future versions may add per-tool authority overrides.

## Approval channels

A runtime MUST support at least `none` (auto-approve everything when running unattended) and SHOULD support at least one human channel (`email`, `slack`, `telegram`, or `in_app`).

`default_timeout` is an ISO 8601 duration. After it elapses, runtimes MUST treat the request as denied and surface that decision in the agent's audit log.

## Audit obligation

Runtimes that operate an agency MUST persist:

- Every action invoked, with agent slug + verb + arguments
- Every approval requested + granted/denied/timeout, with timestamps
- The resource_ceiling consumption to date

Storage location is runtime-defined, but the manifest expects audit data to exist and be retrievable. Validators don't enforce this — runtime test suites do.

## Status

**v1-draft.** Field names and authority verbs are stabilizing. Breaking changes will be documented in `CHANGELOG.md` and require RFC discussion.
