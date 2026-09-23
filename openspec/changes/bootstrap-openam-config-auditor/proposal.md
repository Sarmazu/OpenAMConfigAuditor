# Proposal

## Why

Teams need a safe, reproducible way to determine whether significant PREPROD
OpenAM parameters have corresponding NT parameters and whether matched values
differ, even when large source files use different physical ordering. Before
implementation begins, the project needs reviewed behavioral and architectural
contracts that prevent speculative parsing and unsafe handling of corporate
data.

## What Changes

- Define contracts for accepting PREPROD and NT configuration sources through a
  replaceable parser boundary whose concrete format remains unspecified.
- Define a minimal conceptual canonical-record contract without freezing paths,
  block structure, or override semantics before structural analysis.
- Define order-independent deterministic matching separately from value
  comparison, with explicit match method and value status.
- Define bounded LLM-assisted resolution for ambiguous candidates only.
- Define separate controls for public repository content, local corporate
  reports, and secret redaction across durable outputs.
- Define `result.json` as the authoritative machine-readable result and require
  reproducible rendering from it.
- Define standalone HTML and CSV reporting behaviors for review workflows.
- Define a reproducible corporate Kilo handoff that produces a local,
  synthetic-only structural report and requires manual security approval before
  any transfer into public development.
- Establish a synthetic-data-only repository scaffold and the OpenSpec-driven
  development process.

## Non-goals

This change does not implement a production OpenAM parser, PREPROD/NT adapters,
embeddings, vector search, RAG, a database, MCP, Kubernetes, a web server,
automatic configuration modification or remediation, a cloud backend, SaaS
upload, or production LLM integration. It does not create a fake parser that
claims support for an unknown export format. It does not run the corporate
analysis, publish its local report automatically, or treat Kilo's self-check as
approval for public transfer.

## Corporate-data and security boundary

No real corporate OpenAM configuration or value, including internal hostnames,
IP addresses, URLs, DNs, usernames, secrets, tokens, certificates, or internal
identifiers, may be committed to this public repository. Development and
automated tests use synthetic data only. Kilo will inspect real files in the
corporate environment and provide only an approved sanitized structural
specification to public development. The reproducible instructions live under
`docs/corporate-analysis/`; Kilo writes the candidate report only to the local
corporate `analysis/openam-config-structure.md`. Kilo must not stage, commit,
push, or open a pull request for that report. Manual structural and security
reviews followed by explicit approval are required before a sanitized report
may cross into public development.

Reports generated and retained locally inside the corporate environment may
include non-secret configuration values when they are needed for remediation
and permitted by the active output policy. Secrets and values with unknown
sensitivity remain redacted by default in every durable report, including
`result.json`, HTML, and CSV.

## Capabilities

### New Capabilities

- `configuration-ingestion`: Accept the PREPROD and NT roles through a replaceable parser contract while the concrete source syntax remains blocked.
- `canonical-configuration-model`: Represent parsed configuration concepts as canonical records without prematurely fixing format-specific structure.
- `deterministic-comparison`: Match records independently of source order and report match method separately from value status.
- `ambiguous-candidate-resolution`: Bound and label optional semantic resolution of ambiguous candidate sets.
- `sensitive-data-protection`: Keep corporate values out of the public repository, permit policy-approved non-secret values in local corporate reports, and redact secrets by default in durable outputs.
- `machine-readable-results`: Produce authoritative `result.json` data sufficient for reproducible downstream rendering.
- `human-readable-reporting`: Render standalone HTML review output and focused CSV exports from saved results.

### Modified Capabilities

None. This repository has no existing capability specifications.

## Impact

This establishes contracts for future Python ingestion, normalization,
comparison, optional LLM resolution, and report rendering components. It adds
planning metadata and the corporate-analysis handoff package only; no production
comparison behavior, external service dependency, or corporate-data dependency
is introduced into public development.
