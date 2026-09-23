# Spec Delta

## Purpose

Defines how the auditor receives the two environment roles without assuming an
OpenAM export syntax that has not yet been safely documented.

## ADDED Requirements

### Requirement: Two explicit configuration roles
The auditor SHALL require exactly one configuration source identified as
PREPROD and one identified as NT for a comparison run.

#### Scenario: Both roles are provided
- **WHEN** a caller supplies one readable PREPROD source and one readable NT source through a supported parser
- **THEN** the auditor accepts both sources for canonicalization

#### Scenario: A required role is absent
- **WHEN** either PREPROD or NT is not supplied
- **THEN** the auditor rejects the run with an error that identifies the missing role

### Requirement: Replaceable parser boundary
The auditor SHALL obtain records through a parser contract that is independent
of the concrete source format and SHALL identify which parser handled each
source.

#### Scenario: A supported parser accepts a source
- **WHEN** the configured parser recognizes and successfully reads a source
- **THEN** the auditor records the parser identity and accepts its canonical records

#### Scenario: No parser supports the source
- **WHEN** no configured parser declares support for an input source
- **THEN** the auditor fails explicitly without treating arbitrary text as valid configuration records

### Requirement: Production parsing remains blocked pending structural analysis
The project SHALL NOT claim production OpenAM export support until an approved,
sanitized structural analysis has defined the relevant syntax and semantics.

#### Scenario: Structural analysis is unavailable
- **WHEN** a production OpenAM source is presented before the structural analysis is approved
- **THEN** the auditor reports that production ingestion is unsupported and does not fabricate records

#### Scenario: Local structural report has not been approved
- **WHEN** Kilo has produced a local corporate structural report but manual structural and security reviews have not explicitly approved it for public-development use
- **THEN** production ingestion remains unsupported and no concrete parser semantics are inferred from that report

### Requirement: Ingestion failures are explicit and non-disclosing
The auditor SHALL report unreadable, malformed, or partially parsed sources as
failures without including source values in the error message by default.

#### Scenario: A source cannot be read
- **WHEN** either source cannot be opened or completely processed
- **THEN** the run fails with the affected role and a non-sensitive reason and produces no successful comparison result
