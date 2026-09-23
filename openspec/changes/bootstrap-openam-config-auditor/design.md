# Design

## Context

See `proposal.md` for motivation and scope. The repository currently contains no
comparison engine and no approved description of the real OpenAM export format.
Inputs may be several megabytes, physical ordering is unreliable, and real
corporate values cannot be committed to the public repository. The behavioral
contracts are in `specs/`.

Two development environments are separated by a strict trust boundary:

```text
PUBLIC DEVELOPMENT
Codex + synthetic data
        ^
        | approved sanitized structural specification only
        |
CORPORATE ENVIRONMENT
Kilo + real OpenAM configs
```

Kilo examines real files only inside the corporate environment and produces
`analysis/openam-config-structure.md` or an equivalent sanitized report. Codex
uses that report to plan a later production-compatible parser and matcher.
Implemented releases return to the corporate environment for validation. Raw
configurations and real corporate values never cross into public development.
Inside the corporate environment, locally retained reports may contain
policy-approved non-secret values needed for remediation; those reports remain
corporate artifacts and are never committed to the public repository. Secrets
and values with unknown sensitivity remain redacted by default in all durable
reports.

## Goals / Non-Goals

**Goals:**

- Establish stable boundaries between ingestion, canonicalization,
  deterministic comparison, optional semantic resolution, result persistence,
  and rendering.
- Keep exact outcomes reproducible and usable without an LLM or network.
- Make repository publication controls and corporate report disclosure policy
  enforceable before any durable output boundary.
- Permit multi-megabyte inputs without requiring full configurations to be sent
  to an external component.

**Non-Goals:**

- Select or implement the production OpenAM grammar before Kilo's sanitized
  analysis is approved.
- Finalize format-dependent record paths, block semantics, inheritance,
  duplicate handling, or override behavior.
- Introduce hosted services, automatic remediation, or a persistent database.

## Decisions

### 1. Use a staged, deterministic-first pipeline

The planned data flow is:

```text
PREPROD source ---- parser adapter ---- canonical records --\
                                                            +-> normalization
NT source --------- parser adapter ---- canonical records --/        |
                                                                     v
                                                       deterministic matching
                                                                     |
                                            +------------------------+---------+
                                            |                                  |
                                      exact outcomes                    candidate sets
                                            |                                  |
                                            |                     optional bounded resolver
                                            +------------------------+---------+
                                                                     v
                                                     policy-compliant result.json
                                                                     |
                                                     +---------------+----------+
                                                     v                          v
                                               report.html                 CSV exports
```

Parsing, normalization, candidate generation, exact matching, and value
comparison are deterministic stages. Semantic resolution is an optional branch
only after deterministic processing leaves ambiguity.

Alternative considered: compare both raw files directly with an LLM. Rejected
because it is difficult to reproduce, unsafe for corporate data, costly for
large inputs, and unable to provide a reliable exact-diff contract.

### 2. Use an explicit, review-gated Kilo handoff

The public repository carries only the reusable instructions in
`docs/corporate-analysis/`. An operator clones or pulls the repository into the
corporate environment and supplies Kilo with local PREPROD and NT source paths.
Kilo follows `kilo-structural-analysis-prompt.md`, treats both sources as
read-only, and writes only the local candidate report at
`analysis/openam-config-structure.md`.

The handoff sequence is:

```text
public instructions
-> corporate clone/pull
-> read-only Kilo analysis of PREPROD and NT
-> local candidate structural report
-> Kilo sanitization self-check
-> manual structural review
-> manual security review
-> explicit approval
-> deliberate transfer of the sanitized report to public development
```

Kilo does not stage, commit, push, open a pull request, or otherwise publish the
candidate report. Its self-check is necessary but not sufficient for transfer.
An unapproved report remains corporate-local and does not unblock production
parser design. After an authorized reviewer explicitly approves the report, a
human-controlled transfer may bring only the sanitized structural specification
back into public development.

Alternative considered: let Kilo commit or push a self-checked report directly.
Rejected because automated publication would bypass the independent structural
and security reviews at the corporate/public trust boundary.

### 3. Keep parser adapters behind a format-neutral contract

The ingestion boundary yields validated canonical records plus parser identity
and safe diagnostics. A later change informed by the sanitized structural report
will define real syntax and mapping rules. Initial tests may use an explicitly
test-only synthetic adapter, which must never advertise production OpenAM
compatibility.

Alternative considered: assume a familiar OpenAM export format before
structural analysis. Rejected because the actual corporate export syntax and
semantics have not yet been approved or documented.

### 4. Keep the canonical model minimal and versioned

The conceptual record carries source role, logical identity components, a value
representation, safe provenance, and sensitivity metadata. Exact field shapes
and format-specific identity rules remain deferred. Versioned contracts allow
later structural knowledge to evolve without silently changing comparison
meaning.

Alternative considered: finalize a comprehensive `ConfigRecord` now. Rejected
because that would encode guesses about unknown OpenAM exports.

### 5. Separate correspondence from value equality

Each outcome records `match_method` independently from `value_status`.
Deterministic rules establish exact matches first; candidate generation may
produce unresolved sets but does not silently select among them. Stable sorting
is applied only for output reproducibility, never as a matching heuristic.

Alternative considered: emit only `same`, `different`, and `missing`. Rejected
because it loses how the correspondence was established and makes semantic
decisions indistinguishable from exact ones.

### 6. Treat the semantic resolver as an untrusted optional boundary

Before a resolver call, records are minimized and filtered under the sensitivity
policy. Only one ambiguous record and its bounded candidate set cross the
boundary. Responses are schema-validated and restricted to supplied candidates.
The auditor then applies a configured deterministic acceptance policy; resolver
self-reported confidence is not an acceptance decision. Only outputs that pass
that policy are labeled semantic and accepted. Invalid, unavailable, or
policy-rejected outputs preserve ambiguity. Exact matches never depend on or get
replaced by resolver output. The result records the applicable policy identity
or version so the acceptance decision can be reproduced and reviewed.

Alternative considered: let the resolver see full canonical configurations for
context. Rejected because it expands disclosure, latency, and nondeterminism
without being necessary for exact matching.

### 7. Separate repository publication from corporate report disclosure

Sensitivity classification occurs before any value reaches `result.json`,
logs, diagnostics, HTML, or CSV. Unknown classification fails closed and is
treated like a secret. No real corporate configuration or value is eligible for
commit to the public repository; repository fixtures and examples remain
synthetic.

Within the corporate environment, an explicit output policy may retain
non-secret values in local `result.json`, HTML, and CSV artifacts when reviewers
need those values for remediation. Secrets and unknown-sensitivity values remain
redacted by default in every durable report. Renderers consume the disclosure
decisions already recorded in the result and enforce presentation controls such
as disabling copy for protected values.

Alternative considered: redact every value in every local report. Rejected
because remediation may require actual non-secret configuration values.
Alternative considered: store secrets in `result.json` and redact only in HTML.
Rejected because JSON and CSV are durable disclosure surfaces too.

### 8. Make result.json the rendering boundary

`result.json` is schema-versioned and contains all policy-compliant facts needed
by renderers, including outcome statuses, decision origin, disclosure decisions,
safe grouping data, and run metadata. Rendering never reparses source files or
reinvokes a resolver. HTML is a standalone static artifact with embedded assets;
Reviewed/Fixed state is local presentation state and does not rewrite comparison
facts.

Alternative considered: render reports directly during parsing. Rejected
because reports could not be reproduced independently and parser changes would
be coupled to presentation.

### 9. Bound memory through staged processing

Parser adapters should support incremental record emission, while deterministic
indexes retain only the canonical data needed for matching. Renderers operate
from saved result data. Concrete performance thresholds and external sorting are
deferred until sanitized structural characteristics and synthetic benchmarks
show they are necessary.

Alternative considered: introduce a database or distributed framework now.
Rejected because several-megabyte sources do not justify that complexity without
measurements.

## Risks / Trade-offs

- **[Sanitized analysis omits a structural edge case]** -> Version the parser and
  canonical contracts, reject unknown constructs explicitly, and validate later
  inside the corporate environment.
- **[A secret is misclassified as non-secret]** -> Default unknown values to
  secret-equivalent handling, test every serialization boundary, and keep
  resolver payloads policy-filtered.
- **[A local Kilo report is published before review]** -> Keep the candidate
  report corporate-local, prohibit Kilo VCS publication actions, inspect the
  proposed transfer, and require explicit structural and security approval.
- **[A local corporate remediation report is accidentally committed]** -> Keep
  generated reports out of tracked content and scan proposed changes for any
  real corporate values.
- **[Semantic resolution reduces reproducibility]** -> Preserve method and
  resolver metadata, never use it for exact matches, and keep unresolved output
  valid when the resolver is absent.
- **[Standalone HTML grows with large results]** -> Keep assets compact, avoid
  duplicated source values, and benchmark with large synthetic fixtures before
  considering pagination or split artifacts.
- **[Canonical contract changes after Kilo analysis]** -> Keep this contract
  conceptual now and introduce concrete parsing semantics through a reviewed
  follow-up OpenSpec change.

## Migration Plan

There is no deployed system to migrate. After human approval, implementation
can proceed task-by-task against synthetic fixtures. Production parsing remains
blocked until the sanitized structural analysis is reviewed and a follow-up
change defines the concrete adapter. Rollback during bootstrap is removal of the
new, not-yet-released package artifacts; no corporate data or state is migrated
into public development.
