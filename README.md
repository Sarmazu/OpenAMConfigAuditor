# OpenAMConfigAuditor

**Status: specification / architecture phase**

OpenAMConfigAuditor is intended to compare two large OpenAM configuration
sources, initially PREPROD and NT. It will identify corresponding significant
parameters despite source-order differences and report missing or differing
values.

The planned architecture is deterministic-first:

```text
configuration sources
-> deterministic parser
-> canonical records
-> normalization
-> deterministic matching
-> candidate generation
-> LLM resolver only for ambiguous cases
-> result.json
-> standalone HTML and CSV reports
```

`result.json` is planned as the machine-readable source of truth. An LLM will
not parse full configurations or act as the primary diff engine.

## Security boundary

This repository must contain only synthetic fixtures and sanitized structural
documentation. Never add real corporate OpenAM configurations or values,
internal hostnames, IP addresses, URLs, DNs, usernames, secrets, tokens,
certificates, or internal identifiers. Reports and logs must not expose
plaintext secrets by default.

## Development workflow

Feature development is specification-driven through OpenSpec:

```text
idea/problem -> proposal -> behavioral specs -> design -> tasks
-> human review -> apply -> verify -> sync/archive
```

**NO SPEC -> NO IMPLEMENTATION.** See active changes under `openspec/changes/`.
OpenSpec commands require Node.js 20.19 or newer.

The production parser is blocked until Kilo produces a sanitized structural
analysis of real OpenAM exports. The expected safe handoff is
`analysis/openam-config-structure.md` (or an equivalent approved report). This
project is not yet a working comparison tool.
