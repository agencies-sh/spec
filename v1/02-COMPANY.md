# COMPANY.md (v1)

**Inherited from [agentcompanies/v1](https://agentcompanies.io/specification#companymd) without modification.**

agentagencies/v1 does not redefine `COMPANY.md`. Use the upstream definition verbatim.

## Notes specific to agencies

- A `COMPANY.md` MAY coexist with an `AGENCY.md` at the same root. The agency layer adds runtime contract; the company layer expresses identity + structure.
- If both exist, fields like `name`, `description`, `tags` SHOULD be consistent. A future linter will warn on drift.
- `metadata.sources` is strongly recommended for provenance even when not required by the upstream spec.
