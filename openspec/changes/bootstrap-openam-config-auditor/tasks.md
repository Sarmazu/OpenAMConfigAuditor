# Tasks

## 1. Corporate structural-analysis handoff

- [ ] 1.1 Create and review `docs/corporate-analysis/kilo-structural-analysis-prompt.md`; verify it covers the required structural questions, read-only source handling, synthetic-only examples, and sanitization self-check.
- [ ] 1.2 Document the public-to-corporate-to-public handoff in `docs/corporate-analysis/README.md`; verify the workflow keeps the candidate report local until manual structural and security reviews explicitly approve transfer.
- [ ] 1.3 Enforce the prohibition on automatic Kilo publication; verify the prompt and workflow forbid staging, commit, push, and pull-request creation for `analysis/openam-config-structure.md`.
- [ ] 1.4 Require manual security review before transfer; verify an unapproved or questionable report remains corporate-local and cannot unblock production parser work.

## 2. Contract and test foundation

- [ ] 2.1 Define versioned, format-neutral contracts for source roles, canonical records, comparison outcomes, and policy-compliant result data; verify contract validation tests cover missing required fields and unsupported versions.
- [ ] 2.2 Add a clearly test-only synthetic parser adapter and invented fixtures; verify repository scans and fixture review find no real corporate values or claim of production OpenAM compatibility.
- [ ] 2.3 Add automated checks for deterministic serialization and stable output ordering; verify repeated runs over the same synthetic data produce equivalent artifacts.

## 3. Ingestion and canonicalization boundary

- [ ] 3.1 Implement PREPROD/NT role validation and parser selection boundaries; verify tests cover both roles present, a missing role, an unreadable source, and no supported parser.
- [ ] 3.2 Implement canonical-record validation without format-specific OpenAM assumptions; verify tests reject missing identity, provenance, value, or sensitivity information with non-disclosing errors.
- [ ] 3.3 Enforce an explicit unsupported-production-parser response until approved structural analysis exists; verify a production-format request cannot silently fall back to arbitrary text parsing or use an unapproved Kilo report.

## 4. Deterministic comparison

- [ ] 4.1 Implement deterministic normalization and exact identity matching over canonical records; verify permutations of the same synthetic record sets produce identical correspondences.
- [ ] 4.2 Implement value comparison independently from correspondence; verify tests assert separate `match_method` and `value_status` values for equal and differing pairs.
- [ ] 4.3 Implement missing-in-NT, unmatched-NT, and ambiguous candidate outcomes; verify tests demonstrate that multiple candidates are never silently selected.

## 5. Repository and report data controls

- [ ] 5.1 Implement secret, non-secret, and unknown sensitivity classification with fail-closed handling; verify secret and unknown synthetic values are redacted by default before durable reporting.
- [ ] 5.2 Implement separate public-repository exclusion and local corporate output-policy controls; verify tests allow permitted non-secret remediation values in local reports while repository fixtures remain synthetic and seeded secrets never appear in durable artifacts.
- [ ] 5.3 Define safe-copy eligibility carried to renderers; verify only policy-permitted non-secret values receive copy-enabled output metadata.

## 6. Optional ambiguous resolver boundary

- [ ] 6.1 Implement a resolver-neutral request/response contract limited to one ambiguous record and its bounded, policy-filtered candidates; verify payload tests exclude unrelated records and complete configurations.
- [ ] 6.2 Validate resolver decisions against the supplied candidate set, apply the configured deterministic acceptance policy, and label accepted decisions as semantic; verify invalid, unavailable, and policy-rejected responses preserve ambiguity regardless of resolver self-reported confidence.
- [ ] 6.3 Prevent resolver invocation for exact deterministic matches; verify exact results are identical with the resolver enabled, disabled, or failing.

## 7. Authoritative result artifact

- [ ] 7.1 Implement schema-versioned `result.json` serialization from policy-compliant outcomes; verify schema tests cover statuses, methods, disclosure decisions, safe provenance, grouping metadata, and processing metadata.
- [ ] 7.2 Implement strict loading of saved results; verify unsupported versions and missing required fields fail clearly without producing a report.
- [ ] 7.3 Add a synthetic end-to-end result fixture produced through the pipeline; verify no raw source access or resolver call is needed after the result is saved.

## 8. HTML and CSV rendering

- [ ] 8.1 Render standalone `report.html` with embedded CSS/JavaScript; verify an offline browser test exercises collapse/expand, search, and filters without network requests.
- [ ] 8.2 Implement ACTIONABLE mode and neutral fallback grouping; verify missing, differing, ambiguous, and failed outcomes remain visible while equal outcomes are filtered.
- [ ] 8.3 Implement browser-local Reviewed/Fixed state without mutating comparison facts; verify interaction tests persist local state and leave the source result unchanged.
- [ ] 8.4 Implement safe copy controls; verify browser tests permit copying only policy-approved non-secret values and expose no copy control for secret or unknown-sensitivity values.
- [ ] 8.5 Render `comparison.csv`, `missing-in-nt.csv`, and `ambiguous.csv` from saved results; verify CSV statuses and disclosure decisions match `result.json`.

## 9. Quality and security verification

- [ ] 9.1 Add large synthetic input and result fixtures sized to exercise multi-megabyte processing; verify the pipeline completes within documented local benchmark limits without external services.
- [ ] 9.2 Run the complete automated test, lint, and type-check suites; verify all configured checks pass in a clean local environment using synthetic data only.
- [ ] 9.3 Scan tracked files for any real corporate values and scan generated test artifacts for seeded secrets or prohibited raw inputs; verify both repository-boundary and report-redaction checks pass before marking the change implemented.
- [ ] 9.4 Document verified behavior and remaining production-parser block; verify no task in this change implements concrete OpenAM syntax, paths, blocks, or override semantics before approved Kilo analysis and a reviewed follow-up OpenSpec change.
