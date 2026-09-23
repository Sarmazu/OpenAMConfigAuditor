# Spec Delta

## Purpose

Defines the minimum format-neutral information downstream comparison stages
need while leaving OpenAM-specific structure open for sanitized analysis.

## ADDED Requirements

### Requirement: Canonical records preserve comparison-relevant information
Each canonical record SHALL identify its source role, carry a logical identity
representation, retain a comparison value representation, include provenance
sufficient for safe diagnostics, and include sensitivity classification.

#### Scenario: A parser emits a valid record
- **WHEN** a supported parser extracts a significant configuration item
- **THEN** downstream stages receive its role, conceptual identity, value representation, safe provenance, and sensitivity classification

#### Scenario: Required canonical information is absent
- **WHEN** a parser emits a record missing information required by the canonical contract
- **THEN** the auditor rejects that record or run with a non-disclosing validation error rather than silently inventing data

### Requirement: Unknown OpenAM semantics remain unspecified
The canonical contract SHALL NOT assign format-specific block, path, duplicate,
multi-value, inheritance, or override meaning until those semantics are defined
by approved structural analysis.

#### Scenario: A source feature has no approved semantic rule
- **WHEN** parsing encounters a construct whose meaning is not defined by the approved structural specification
- **THEN** the construct is reported as unsupported or unresolved rather than coerced into an assumed canonical identity

### Requirement: Canonicalization is deterministic
Given the same supported inputs, parser version, and normalization policy, the
auditor SHALL produce equivalent canonical records regardless of repeated runs.

#### Scenario: Canonicalization is repeated
- **WHEN** the same source is processed twice with the same declared versions and policy
- **THEN** the resulting canonical record content is equivalent
