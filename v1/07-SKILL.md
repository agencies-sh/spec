# SKILL.md (v1)

**Defined by Anthropic Skills / agentcompanies/v1 — must not be redefined.**

Per agentcompanies/v1: *"Agent Companies must not redefine SKILL.md."*

agentagencies/v1 honors that rule. SKILLs are referenced by their existing format and live under `skills/<slug>/SKILL.md` (canonical layout).

## Provenance

When SKILLs are vendored from external sources (e.g., a third-party repo), the agency's `COMPANY.md` and/or `agents/<slug>/AGENTS.md` SHOULD include:

```yaml
metadata:
  sources:
    - repository: https://github.com/upstream/source-repo
      commit: <pinned-sha>
      path: skills/<slug>/SKILL.md
      mode: include
      license: MIT
```

This makes vendoring traceable and enables tooling like `sync-upstream.sh` to bump pinned SHAs reproducibly.
