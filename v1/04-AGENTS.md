# AGENTS.md (v1)

**Inherited from agentcompanies/v1.** Agencies layer adds optional fields:

```yaml
metadata:
  authority:                     # overrides AGENCY.md.authority.by_agent.<slug>
    web_search: auto
  resource_ceiling:              # overrides AGENCY.md.runtime.resource_ceilings
    credits_per_task: 200
```

When both `AGENCY.md` and an agent declare ceilings/authority, the **agent-local** value wins. Conflicts are not errors — they're how an agency expresses "this agent is more restricted than default".
