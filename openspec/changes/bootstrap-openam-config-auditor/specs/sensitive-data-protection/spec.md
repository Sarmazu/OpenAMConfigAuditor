# Spec Delta

## Purpose

Defines separate controls for public repository content, policy-approved local
corporate reports, and default secret redaction in durable artifacts.

## ADDED Requirements

### Requirement: Public repository contains no real corporate values
No real corporate configuration source, value, identifier, endpoint, or secret
SHALL be committed to the public repository. Repository documentation, fixtures,
tests, and checked-in examples SHALL use only synthetic or approved sanitized
structural information.

#### Scenario: Development fixture is prepared
- **WHEN** a fixture or example is added to the public repository
- **THEN** it contains only invented values and no value derived from a real corporate configuration

#### Scenario: Generated corporate report exists locally
- **WHEN** a report is generated from real configurations inside the corporate environment
- **THEN** the report remains a local corporate artifact and is not treated as content eligible for repository commit

### Requirement: Structural-analysis transfer requires explicit review
A Kilo structural-analysis report SHALL remain local to the corporate
environment until manual structural and security reviews confirm that it
contains no real corporate values or sensitive data and an authorized reviewer
explicitly approves transfer into public development. Kilo SHALL NOT stage,
commit, push, or open a pull request for the report automatically.

#### Scenario: Kilo completes its self-check
- **WHEN** Kilo reports that its sanitization self-check passed
- **THEN** the report remains local and unapproved until the required human reviews and explicit approval are complete

#### Scenario: Report passes human review and receives explicit approval
- **WHEN** manual structural and security reviews pass and an authorized reviewer explicitly approves the sanitized report
- **THEN** the report may be transferred deliberately into public development without any automatic Kilo commit, push, or pull request

#### Scenario: Review finds or suspects corporate data
- **WHEN** a reviewer finds or cannot rule out a real corporate value, identifier, or sensitive datum in the report
- **THEN** transfer is prohibited and the report remains inside the corporate environment for correction and re-review

### Requirement: Values receive sensitivity classification
Every value eligible for output SHALL be classified as secret, non-secret, or
unknown before it is serialized, rendered, logged, or offered for copying.

#### Scenario: A value is classified as secret
- **WHEN** output is produced for a value classified as secret
- **THEN** plaintext is omitted or replaced with an explicit redacted representation by default

#### Scenario: Classification is unknown
- **WHEN** the auditor cannot determine whether a value is safe to disclose
- **THEN** output handling treats that value as sensitive by default

### Requirement: Local corporate reports may contain non-secret values
The auditor SHALL allow an explicitly configured corporate output policy to
include non-secret configuration values in locally retained `result.json`, HTML,
and CSV reports when those values are needed for remediation.

#### Scenario: Remediation requires a non-secret value
- **WHEN** a value is classified as non-secret and the active corporate output policy permits its disclosure
- **THEN** local corporate reports may include the value while remaining excluded from repository commits

### Requirement: Durable reports redact secrets by default
Machine-readable results, HTML, CSV, diagnostics, and logs SHALL exclude
plaintext secrets and values with unknown sensitivity by default, including
when non-secret values are permitted by a local corporate output policy.

#### Scenario: Secret matched values differ
- **WHEN** a PREPROD and NT pair contains differing secret values
- **THEN** outputs may indicate that they differ but do not include either plaintext value

#### Scenario: Processing fails near protected content
- **WHEN** a parse, normalization, comparison, or rendering error involves secret or unknown-sensitivity content
- **THEN** the diagnostic identifies the stage and safe provenance without embedding the content

### Requirement: Copy actions are limited to permitted non-secret values
Human-readable output SHALL offer value-copy behavior only for values classified
as non-secret and permitted for disclosure by the active corporate output
policy.

#### Scenario: User views a secret result
- **WHEN** a report row contains redacted secret or unknown-sensitivity data
- **THEN** the report provides no control that copies the plaintext value
