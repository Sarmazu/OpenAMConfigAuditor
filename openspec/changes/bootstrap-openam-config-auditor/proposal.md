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
- Define default redaction of sensitive values across durable outputs.
- Define `result.json` as the authoritative machine-readable result and require
  reproducible rendering from it.
- Define standalone HTML and CSV reporting behaviors for review workflows.
- Establish a synthetic-data-only repository scaffold and the OpenSpec-driven
  development process.

## Non-goals

This change does not implement a production OpenAM parser, PREPROD/NT adapters,
embeddings, vector search, RAG, a database, MCP, Kubernetes, a web server,
automatic configuration modification or remediation, a cloud backend, SaaS
upload, or production LLM integration. It does not create a fake parser that
claims support for an unknown export format.

## Corporate-data and security boundary

Real OpenAM configurations and values, internal hostnames, IP addresses, URLs,
DNs, usernames, secrets, tokens, certificates, and internal identifiers must
not enter this repository. Development and automated tests use synthetic data
only. Kilo will inspect real files in the corporate environment and provide
only an approved sanitized structural specification; plaintext sensitive values
must be excluded from reports by default.

## Capabilities

### New Capabilities

- `configuration-ingestion`: Accept the PREPROD and NT roles through a replaceable parser contract while the concrete source syntax remains blocked.
- `canonical-configuration-model`: Represent parsed configuration concepts as canonical records without prematurely fixing format-specific structure.
- `deterministic-comparison`: Match records independently of source order and report match method separately from value status.
- `ambiguous-candidate-resolution`: Bound and label optional semantic resolution of ambiguous candidate sets.
- `sensitive-data-protection`: Classify sensitive values and prevent plaintext disclosure in outputs by default.
- `machine-readable-results`: Produce authoritative `result.json` data sufficient for reproducible downstream rendering.
- `human-readable-reporting`: Render standalone HTML review output and focused CSV exports from saved results.

### Modified Capabilities

None. This repository has no existing capability specifications.

## Impact

This establishes contracts for future Python ingestion, normalization,
comparison, optional LLM resolution, and report rendering components. It adds
planning and project metadata only; no production comparison behavior, external
service dependency, or corporate-data dependency is introduced.
