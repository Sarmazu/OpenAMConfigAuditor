# Spec Delta

## Purpose

Defines a versioned, authoritative result artifact that supports auditability
and repeatable report generation without rereading source configurations.

## ADDED Requirements

### Requirement: Result JSON is the authoritative comparison artifact
A successful comparison SHALL produce a schema-versioned `result.json` that
contains the sanitized comparison outcomes, statuses, methods, safe provenance,
and processing metadata required by supported renderers.

#### Scenario: Comparison succeeds
- **WHEN** both sources are processed and the comparison completes
- **THEN** a valid `result.json` is produced as the source of truth for all report formats

#### Scenario: Comparison cannot complete
- **WHEN** a fatal ingestion or contract-validation error occurs
- **THEN** the auditor does not publish a result that is presented as a successful complete comparison

### Requirement: Rendering is reproducible from saved results
Supported reports SHALL be regenerable from a valid saved `result.json` without
reparsing PREPROD or NT sources and without invoking a resolver.

#### Scenario: Original source files are unavailable
- **WHEN** a supported renderer receives a complete valid saved result
- **THEN** it can regenerate semantically equivalent reports without access to either source file

### Requirement: Invalid result data is rejected
Consumers SHALL validate the result schema version and required content before
rendering or exporting.

#### Scenario: Schema version is unsupported
- **WHEN** a renderer receives a result with an unsupported schema version
- **THEN** it fails with a clear non-sensitive compatibility error instead of guessing the structure

#### Scenario: Required result fields are missing
- **WHEN** a result artifact is incomplete or malformed
- **THEN** the consumer rejects it and does not present a misleading report
