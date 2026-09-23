# Tasks

## 1. Contract and test foundation

- [ ] 1.1 Define versioned, format-neutral contracts for source roles, canonical records, comparison outcomes, and sanitized result data; verify contract validation tests cover missing required fields and unsupported versions.
- [ ] 1.2 Add a clearly test-only synthetic parser adapter and invented fixtures; verify repository scans and fixture review find no corporate values or claim of production OpenAM compatibility.
- [ ] 1.3 Add automated checks for deterministic serialization and stable output ordering; verify repeated runs over the same synthetic data produce equivalent artifacts.

## 2. Ingestion and canonicalization boundary

- [ ] 2.1 Implement PREPROD/NT role validation and parser selection boundaries; verify tests cover both roles present, a missing role, an unreadable source, and no supported parser.
- [ ] 2.2 Implement canonical-record validation without format-specific OpenAM assumptions; verify tests reject missing identity, provenance, value, or sensitivity information with non-disclosing errors.
- [ ] 2.3 Enforce an explicit unsupported-production-parser response until approved structural analysis exists; verify a production-format request cannot silently fall back to arbitrary text parsing.

## 3. Deterministic comparison

- [ ] 3.1 Implement deterministic normalization and exact identity matching over canonical records; verify permutations of the same synthetic record sets produce identical correspondences.
- [ ] 3.2 Implement value comparison independently from correspondence; verify tests assert separate `match_method` and `value_status` values for equal and differing pairs.
- [ ] 3.3 Implement missing-in-NT, unmatched-NT, and ambiguous candidate outcomes; verify tests demonstrate that multiple candidates are never silently selected.

## 4. Sensitive-data controls

- [ ] 4.1 Implement sensitivity classification with fail-closed handling for unknown classifications; verify unknown and sensitive synthetic values are redacted before persistence.
- [ ] 4.2 Apply redaction to result serialization, diagnostics, and logging; verify automated leak tests cannot find seeded synthetic secrets in any captured output.
- [ ] 4.3 Define the safe-copy eligibility carried to renderers; verify sensitive or unknown values never receive copy-enabled output metadata.

## 5. Optional ambiguous resolver boundary

- [ ] 5.1 Implement a resolver-neutral request/response contract limited to one ambiguous record and its bounded, policy-filtered candidates; verify payload tests exclude unrelated records and complete configurations.
- [ ] 5.2 Validate resolver decisions against the supplied candidate set and label accepted decisions as semantic; verify invalid, unavailable, and low-confidence responses preserve ambiguity.
- [ ] 5.3 Prevent resolver invocation for exact deterministic matches; verify exact results are identical with the resolver enabled, disabled, or failing.

## 6. Authoritative result artifact

- [ ] 6.1 Implement schema-versioned `result.json` serialization from sanitized outcomes; verify schema tests cover statuses, methods, safe provenance, grouping metadata, and processing metadata.
- [ ] 6.2 Implement strict loading of saved results; verify unsupported versions and missing required fields fail clearly without producing a report.
- [ ] 6.3 Add a synthetic end-to-end result fixture produced through the pipeline; verify no raw source access or resolver call is needed after the result is saved.

## 7. HTML and CSV rendering

- [ ] 7.1 Render standalone `report.html` with embedded CSS/JavaScript; verify an offline browser test exercises collapse/expand, search, and filters without network requests.
- [ ] 7.2 Implement ACTIONABLE mode and neutral fallback grouping; verify missing, differing, ambiguous, and failed outcomes remain visible while equal outcomes are filtered.
- [ ] 7.3 Implement browser-local Reviewed/Fixed state without mutating comparison facts; verify interaction tests persist local state and leave the source result unchanged.
- [ ] 7.4 Implement safe copy controls; verify browser tests permit copying non-sensitive values and expose no copy control for protected values.
- [ ] 7.5 Render `comparison.csv`, `missing-in-nt.csv`, and `ambiguous.csv` from saved results; verify CSV statuses and redactions match `result.json`.

## 8. Quality and security verification

- [ ] 8.1 Add large synthetic input and result fixtures sized to exercise multi-megabyte processing; verify the pipeline completes within documented local benchmark limits without external services.
- [ ] 8.2 Run the complete automated test, lint, and type-check suites; verify all configured checks pass in a clean local environment using synthetic data only.
- [ ] 8.3 Scan tracked files and generated test artifacts for seeded secrets and prohibited raw inputs; verify the security check passes before marking the change implemented.
- [ ] 8.4 Document verified behavior and remaining production-parser block; verify no task in this change implements concrete OpenAM syntax, paths, blocks, or override semantics before Kilo analysis and a reviewed follow-up OpenSpec change.
