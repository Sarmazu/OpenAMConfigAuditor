# Spec Delta

## Purpose

Defines reproducible, order-independent matching and value comparison as
separate observable decisions with explicit unresolved outcomes.

## ADDED Requirements

### Requirement: Matching is independent of physical source order
The auditor SHALL produce equivalent record correspondences when only the
physical ordering of records or logical blocks changes.

#### Scenario: Inputs are reordered
- **WHEN** semantically equivalent PREPROD and NT record sets are supplied in different physical orders
- **THEN** deterministic matching produces the same correspondences and statuses

### Requirement: Match method and value status are separate
Every comparison outcome SHALL report how correspondence was established as
`match_method` separately from the comparison of values as `value_status`.

#### Scenario: Exact identity with differing values
- **WHEN** deterministic identity rules pair PREPROD and NT records whose normalized values differ
- **THEN** the outcome records the deterministic match method and a differing value status

#### Scenario: Exact identity with equal values
- **WHEN** deterministic identity rules pair records whose normalized values are equal
- **THEN** the outcome records the deterministic match method and an equal value status

### Requirement: Missing and unresolved records are not guessed
The auditor SHALL distinguish an unmatched PREPROD record, an unmatched NT
record, and a record with multiple plausible candidates from a confirmed match.

#### Scenario: PREPROD record has no candidate
- **WHEN** no NT record satisfies the approved deterministic identity rules
- **THEN** the PREPROD record is reported as missing in NT

#### Scenario: Multiple candidates remain
- **WHEN** deterministic processing yields more than one plausible NT candidate and cannot select exactly one
- **THEN** the result remains ambiguous unless an enabled resolver returns a separately labeled decision

### Requirement: Exact deterministic matches do not depend on an LLM
The auditor SHALL complete exact deterministic matching and value comparison
without requiring an LLM or network service.

#### Scenario: No LLM is configured
- **WHEN** all records are resolved by exact deterministic rules and no LLM is available
- **THEN** the auditor produces the same exact results as it would with an LLM available
