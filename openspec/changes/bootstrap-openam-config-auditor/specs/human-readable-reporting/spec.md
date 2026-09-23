# Spec Delta

## Purpose

Defines portable HTML and focused CSV views that help corporate reviewers find
and remediate differences while preserving the configured disclosure policy.

## ADDED Requirements

### Requirement: Standalone HTML report
The primary human-readable artifact SHALL be `report.html`, containing its
required CSS and JavaScript locally and requiring no CDN or web server for
normal review.

#### Scenario: Report is opened offline
- **WHEN** a reviewer opens `report.html` in a supported browser without network access
- **THEN** navigation, search, filtering, and review-state interactions remain available

### Requirement: Review-oriented navigation
The HTML report SHALL group results by logical block when such grouping is
defined, support collapse and expand, provide search and filters, and provide an
ACTIONABLE mode for outcomes requiring attention.

#### Scenario: Reviewer selects ACTIONABLE mode
- **WHEN** ACTIONABLE mode is enabled
- **THEN** equal non-actionable results are hidden and missing, differing, ambiguous, and failed outcomes remain discoverable

#### Scenario: Logical blocks are not defined
- **WHEN** the approved source semantics provide no logical block grouping
- **THEN** the report uses a neutral grouping without inventing OpenAM block semantics

### Requirement: Local review state
The HTML report SHALL let a reviewer mark visible outcomes as Reviewed or Fixed
using browser-local state without changing the authoritative comparison facts
in `result.json`.

#### Scenario: Reviewer updates an item
- **WHEN** a reviewer marks an outcome Reviewed or Fixed
- **THEN** the state is retained locally for that report and the underlying match and value statuses remain unchanged

### Requirement: Safe value copying
The HTML report SHALL allow copying non-secret values only when the active
corporate output policy permits their disclosure and SHALL enforce the
sensitive-data protection contract for every copy interaction.

#### Scenario: Reviewer copies an allowed value
- **WHEN** a value is classified as non-secret, permitted by the active corporate output policy, and the reviewer activates its copy control
- **THEN** the displayed non-secret value is copied

### Requirement: Focused CSV exports
Supported result rendering SHALL produce `comparison.csv`, `missing-in-nt.csv`,
and `ambiguous.csv` with consistent policy-compliant data derived from
`result.json`.

#### Scenario: CSV reports are generated
- **WHEN** the renderer processes a valid saved result
- **THEN** each CSV contains the applicable outcomes and uses the same statuses and disclosure decisions as the authoritative result
