# Spec Delta

## Purpose

Defines classification, redaction, and safe handling rules that prevent
corporate values and secrets from leaking through persistent artifacts.

## ADDED Requirements

### Requirement: Values receive sensitivity classification
Every value eligible for output SHALL have a sensitivity classification before
it is serialized, rendered, logged, or offered for copying.

#### Scenario: A value is classified as sensitive
- **WHEN** output is produced for a value classified as sensitive
- **THEN** plaintext is omitted or replaced with an explicit redacted representation by default

#### Scenario: Classification is unknown
- **WHEN** the auditor cannot determine whether a value is safe to disclose
- **THEN** output handling treats that value as sensitive by default

### Requirement: Default artifacts do not disclose sensitive plaintext
Machine-readable results, HTML, CSV, diagnostics, and logs SHALL exclude
plaintext sensitive values by default.

#### Scenario: Sensitive matched values differ
- **WHEN** a PREPROD and NT pair contains differing sensitive values
- **THEN** outputs may indicate that they differ but do not include either plaintext value

#### Scenario: Processing fails near a sensitive value
- **WHEN** a parse, normalization, comparison, or rendering error involves sensitive content
- **THEN** the diagnostic identifies the stage and safe provenance without embedding the content

### Requirement: Copy actions are limited to non-sensitive values
Human-readable output SHALL offer value-copy behavior only for values classified
as non-sensitive under the active policy.

#### Scenario: User views a sensitive result
- **WHEN** a report row contains redacted sensitive data
- **THEN** the report provides no control that copies the plaintext value
