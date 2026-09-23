# Spec Delta

## Purpose

Constrains optional semantic assistance to bounded ambiguous candidate sets and
makes its decisions distinguishable from deterministic results.

## ADDED Requirements

### Requirement: Resolver input is minimized
An enabled semantic resolver SHALL receive only the ambiguous source record,
its bounded candidate set, and the minimum approved context needed to decide;
it SHALL NOT receive either complete configuration.

#### Scenario: An ambiguous candidate set is submitted
- **WHEN** deterministic processing cannot choose among multiple candidates and semantic resolution is enabled
- **THEN** the resolver receives only the relevant minimized and policy-filtered subset

### Requirement: Semantic decisions are explicitly labeled
A resolver decision SHALL be marked as semantic or ambiguous resolution and
SHALL remain distinguishable from every deterministic match method.

#### Scenario: Resolver selects one candidate
- **WHEN** the resolver selects a candidate under its declared acceptance policy
- **THEN** the result identifies the semantic match method and retains decision metadata sufficient for review

### Requirement: Resolver failure preserves ambiguity
The auditor SHALL retain a deterministic ambiguous outcome when the resolver is
disabled, unavailable, invalid, or insufficiently confident.

#### Scenario: Resolver is unavailable
- **WHEN** an ambiguous set exists and the configured resolver cannot be called
- **THEN** the run remains usable and reports the set as unresolved ambiguity

#### Scenario: Resolver returns an invalid candidate
- **WHEN** a resolver selects an item outside the supplied candidate set
- **THEN** the auditor rejects that decision and preserves the ambiguous outcome

### Requirement: Resolver cannot override exact matching
The auditor SHALL NOT use a semantic resolver as the sole basis to replace an
exact deterministic correspondence.

#### Scenario: Exact match already exists
- **WHEN** deterministic rules establish an exact correspondence
- **THEN** that correspondence is not sent to the resolver for replacement
