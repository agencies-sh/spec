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

---

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

### Verb resolution order

When the runtime is about to invoke a verb on behalf of an agent, it resolves the authority value as follows:

1. **Per-agent override** in `AGENCY.md > authority.by_agent.<agent>.<verb>` (if present).
2. **Agent-local override** in `agents/<slug>/AGENTS.md > metadata.authority.<verb>` (if present).
3. **Agency default** in `AGENCY.md > authority.default`.
4. **Spec default**: `ask`.

The most-restrictive value wins on conflict — an agent-local `ask` overrides an agency-level `auto`. This is intentional: agents can lock themselves down further than the agency permits, never the reverse.

### Worked example — a 3-agent content team

```yaml
authority:
  default: ask                    # spec-restrictive default
  by_agent:
    researcher:
      # Reads + research are auto. Anything that produces output asks.
      web_search: auto
      web_fetch: auto
      file_read: auto
      tool_call: auto             # scoped to researcher's tools list
      file_write: ask
      send_message_internal: auto
      send_message_external: ask
    drafter:
      # Reads existing work, drafts to disk, but doesn't publish.
      web_search: auto
      file_read: auto
      file_write: auto            # writes drafts to drafts/
      tool_call: ask              # publishing tools always ask
      send_message_internal: auto
      send_message_external: ask
      publish_post: ask
    publisher:
      # Most locked-down agent. Every external action confirms.
      file_read: auto
      file_write: ask
      tool_call: ask
      send_message_internal: auto
      send_message_external: ask
      publish_post: ask           # extra-explicit even though `ask` is default
      transact: ask
```

Net effect: `researcher` and `drafter` move work forward independently; `publisher` always pauses for human approval before anything goes external. A single human reviews + approves once per piece.

---

## Heartbeat

`runtime.heartbeat` is an [RFC 5545 RRULE](https://datatracker.ietf.org/doc/html/rfc5545#section-3.3.10) string. The runtime fires a `tick` event at every occurrence.

### What happens at a tick

When the heartbeat fires, the runtime:

1. **Loads the agency state** — task queue, project status, recent audit log.
2. **Invokes the manager agent** (the `manager` field on each TEAM, or a designated agent if unset) with a tick context: `{ now: <timestamp>, due_tasks: [...], blocked_tasks: [...], recent_decisions: [...] }`.
3. **The manager decides** which tasks need attention this tick: assigning, escalating, resurfacing for approval, marking done.
4. **Subordinate agents act** within their authority. Anything outside authority queues an approval request.

### Worked example — daily 9am tick

```yaml
runtime:
  heartbeat: "FREQ=DAILY;BYHOUR=9;BYMINUTE=0"
  timezone: Europe/Paris
```

At **9:00 Europe/Paris** every day:

1. Runtime checks resource ceilings — if `credits_per_day` already exhausted, log + skip.
2. Loads queued tasks where `dueAt < now + 24h` or status = `in_progress`.
3. CEO agent receives the tick context, picks the day's priorities, delegates.
4. Each delegated task runs to completion or hits an approval gate.
5. Approval requests fire to the configured channel (e.g., Telegram).

### Common heartbeat patterns

| RRULE | Cadence |
|---|---|
| `FREQ=DAILY;BYHOUR=9` | Once a day, 9am |
| `FREQ=HOURLY;INTERVAL=4` | Every 4 hours |
| `FREQ=WEEKLY;BYDAY=MO;BYHOUR=9` | Mondays 9am |
| `FREQ=MONTHLY;BYMONTHDAY=1;BYHOUR=9` | First of the month |
| (no `heartbeat` field) | No proactive ticks — agency runs only on operator-initiated requests |

---

## Approval channels

A runtime MUST support at least `none` (auto-approve everything when running unattended) and SHOULD support at least one human channel (`email`, `slack`, `telegram`, or `in_app`).

### Approval lifecycle

```
agent invokes verb V
       ↓
authority resolution → ask
       ↓
runtime constructs approval request
       ↓
delivers to approvals.channel (e.g., Telegram message)
       ↓
       ├─→ human replies "approve" → verb executes, audit log records APPROVED
       ├─→ human replies "deny"    → verb does NOT execute, audit log records DENIED
       └─→ no reply within default_timeout → audit log records TIMEOUT_DENIED
```

### Timeout semantics

`default_timeout` is an ISO 8601 duration. After it elapses without explicit grant, runtimes MUST:

1. Treat the request as **denied**.
2. Persist the timeout outcome in the audit log with the original request payload, the channel/recipient, and the timeout duration.
3. Notify the agent that requested it (so the agent can fall back to an alternative path or report the blocker upstream).
4. NOT re-prompt automatically. Re-prompts are the agent's decision based on the timeout result.

### Per-task timeout overrides

A specific task MAY override `default_timeout` via `metadata.approval_timeout` in its TASK.md. Example:

```yaml
# tasks/urgent-vat-deadline/TASK.md
metadata:
  approval_timeout: PT2H    # 2 hours, not the default 3 days
```

The most-restrictive value wins: if AGENCY default is `P3D` and task-level is `PT2H`, the runtime uses `PT2H`.

---

## Resource ceilings

`runtime.resource_ceilings` caps spend per dimension. Runtimes MUST enforce these caps.

```yaml
resource_ceilings:
  credits_per_day: 10000           # token-credit budget
  credits_per_task: 500            # cap per individual task invocation
  cost_eur_per_day: 5.00           # absolute spend cap (LLM API + tools)
```

### What "enforce" means

When an agent attempts an action that would exceed any ceiling:

1. Runtime **blocks the action** before the underlying LLM call or tool call.
2. Logs the attempted-but-blocked action in the audit log.
3. Notifies the operator via the approval channel: "Daily budget exceeded. Lift cap or wait until reset."
4. The next heartbeat (or the next operator interaction) re-evaluates whether to proceed.

### Per-agent ceilings

An agent MAY override the agency-level ceiling via `metadata.resource_ceiling` in its AGENTS.md, **provided the override is more restrictive**. Runtimes MUST reject overrides that exceed the agency cap.

```yaml
# agents/research-analyst/AGENTS.md
metadata:
  resource_ceiling:
    credits_per_task: 200          # tighter than agency-level 500 — allowed
    cost_eur_per_day: 10.00        # exceeds agency-level 5.00 — REJECTED
```

---

## Audit obligation

Runtimes that operate an agency MUST persist:

- Every action invoked, with agent slug + verb + arguments + timestamp.
- Every approval requested + granted/denied/timeout, with timestamps + channel + recipient.
- The `resource_ceiling` consumption to date (resets per the ceiling's window).

Storage location is runtime-defined. The manifest expects audit data to exist and be retrievable. Validators don't enforce this — runtime test suites do.

### Minimum audit record shape

```json
{
  "timestamp": "2027-04-15T09:00:00+02:00",
  "agency": "my-agency",
  "agent": "researcher",
  "verb": "web_fetch",
  "args": { "url": "https://example.com/data" },
  "authority_resolution": "agent_override:auto",
  "result": "succeeded",
  "credits_used": 14,
  "cost_eur": 0.012,
  "audit_record_id": "audit-2027-04-15-000142"
}
```

Runtimes are free to add fields. The above is the floor — anything less is non-conforming.

---

## Status

**v1-draft.** Field names, authority verbs, and audit shape are stabilizing. Breaking changes will be documented in `CHANGELOG.md` and require RFC discussion.
