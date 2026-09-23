# Design

## Context

See `proposal.md` for motivation and scope. The repository currently contains no
comparison engine and no approved description of the real OpenAM export format.
Inputs may be several megabytes, physical ordering is unreliable, and real
corporate data cannot enter public development. The behavioral contracts are in
`specs/`.

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
configurations and real values never cross the boundary in either direction.

## Goals / Non-Goals

**Goals:**

- Establish stable boundaries between ingestion, canonicalization,
  deterministic comparison, optional semantic resolution, result persistence,
  and rendering.
- Keep exact outcomes reproducible and usable without an LLM or network.
- Make sensitive-data handling enforceable before any durable output boundary.
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
                                                          sanitized result.json
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

### 2. Keep parser adapters behind a format-neutral contract

The ingestion boundary yields validated canonical records plus parser identity
and safe diagnostics. A later change informed by the sanitized structural report
will define real syntax and mapping rules. Initial tests may use an explicitly
test-only synthetic adapter, which must never advertise production OpenAM
compatibility.

Alternative considered: assume the existing README's reference to LDIF defines
the format. Rejected because one historical sentence is not an approved
structural specification and does not define block or override semantics.

### 3. Keep the canonical model minimal and versioned

The conceptual record carries source role, logical identity components, a value
representation, safe provenance, and sensitivity metadata. Exact field shapes
and format-specific identity rules remain deferred. Versioned contracts allow
later structural knowledge to evolve without silently changing comparison
meaning.

Alternative considered: finalize a comprehensive `ConfigRecord` now. Rejected
because that would encode guesses about unknown OpenAM exports.

### 4. Separate correspondence from value equality

Each outcome records `match_method` independently from `value_status`.
Deterministic rules establish exact matches first; candidate generation may
produce unresolved sets but does not silently select among them. Stable sorting
is applied only for output reproducibility, never as a matching heuristic.

Alternative considered: emit only `same`, `different`, and `missing`. Rejected
because it loses how the correspondence was established and makes semantic
decisions indistinguishable from exact ones.

### 5. Treat the semantic resolver as an untrusted optional boundary

Before a resolver call, records are minimized and filtered under the sensitivity
policy. Only one ambiguous record and its bounded candidate set cross the
boundary. Responses are schema-validated, restricted to supplied candidates,
policy-checked, and labeled semantic. Failure preserves ambiguity. Exact matches
never depend on or get replaced by resolver output.

Alternative considered: let the resolver see full canonical configurations for
context. Rejected because it expands disclosure, latency, and nondeterminism
without being necessary for exact matching.

### 6. Enforce redaction before persistence and rendering

Sensitivity classification occurs before any value reaches `result.json`,
logs, diagnostics, HTML, or CSV. Unknown classification fails closed and is
treated as sensitive. Renderers consume already sanitized result data and also
enforce presentation controls such as disabling copy for protected values.

Alternative considered: store plaintext in `result.json` and redact only in
HTML. Rejected because JSON and CSV are durable disclosure surfaces too.

### 7. Make result.json the rendering boundary

`result.json` is schema-versioned and contains all sanitized facts needed by
renderers, including outcome statuses, decision origin, safe grouping data, and
run metadata. Rendering never reparses source files or reinvokes a resolver.
HTML is a standalone static artifact with embedded assets; Reviewed/Fixed state
is local presentation state and does not rewrite comparison facts.

Alternative considered: render reports directly during parsing. Rejected
because reports could not be reproduced independently and parser changes would
be coupled to presentation.

### 8. Bound memory through staged processing

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
- **[Sensitive data is misclassified]** -> Default unknown values to sensitive,
  test every serialization boundary, and keep resolver payloads policy-filtered.
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
new, not-yet-released package artifacts; no corporate data or state is migrated.
