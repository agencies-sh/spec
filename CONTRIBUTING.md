# Contributing to agentagencies/v1

This is a specification, not a library. Changes follow an RFC-style process.

## For typo fixes, clarifications, broken links

Open a PR. Quick reviews.

## For substantive changes (new fields, new manifests, behavior changes)

1. **Open an issue first.** Describe the use case + the proposed change. Tag it `rfc:`.
2. **Wait at least 7 days** for community feedback before opening a PR.
3. **Open the PR** with the spec change AND a CHANGELOG entry.
4. **Update reference implementations** in [openagentik/companies](https://github.com/openagentik/companies) in a companion PR.

## What we won't accept

- Vendor-specific assumptions (a particular LLM, MCP catalog, billing system).
- Breaking changes without a deprecation path.
- Changes that conflict with [agentcompanies/v1](https://agentcompanies.io/specification) without a strong reason + upstream coordination.

## Discussion

[Issues](https://github.com/agencies-sh/spec/issues) are the primary forum.

## License

Contributions are licensed CC-BY-4.0 (see [LICENSE](LICENSE)).
