# Kilo task: sanitized OpenAM configuration structural analysis

## Role and objective

You are Kilo operating entirely inside an approved corporate environment. Your
task is to inspect two real OpenAM configuration sources, PREPROD and NT, and
produce a sanitized structural specification that public development can use to
design a deterministic parser and comparison engine.

This is a read-only analysis task. Do not implement the auditor and do not
modify either source configuration.

## Local inputs and output

The operator will supply local paths for:

- `PREPROD_CONFIG_PATH`
- `NT_CONFIG_PATH`

Do not record the real paths or filenames in the report, commands copied into
the report, logs intended for transfer, or chat output.

Write the result only to this local path inside the corporate clone:

```text
analysis/openam-config-structure.md
```

If that file already exists, stop and ask the operator before overwriting it.
Do not create a second report elsewhere unless the operator explicitly approves
a safe local path.

## Non-negotiable safety rules

1. Treat PREPROD and NT source files as read-only. Do not edit, reformat,
   normalize in place, rename, move, delete, or change their metadata.
2. Do not stage or commit any file. Do not push, create a branch for
   publication, open a pull request, or upload the report or source material.
3. Do not send source content to external SaaS, public services, or external
   models. Perform the analysis within the approved corporate environment.
4. Do not reproduce source excerpts, even short ones, when they contain real
   corporate information.
5. Do not include real configuration values, hostnames, IP addresses, URLs,
   DNs, usernames, credentials, passwords, secrets, tokens, certificates,
   private keys, or internal identifiers in the report or transferable logs.
6. Use only visibly synthetic placeholders, such as:

   ```text
   <ENVIRONMENT>
   <BLOCK_TYPE>
   <PARAMETER_NAME>
   <NON_SECRET_VALUE>
   <SECRET_REDACTED>
   <HOST_EXAMPLE>
   <IP_EXAMPLE>
   <URL_EXAMPLE>
   <DN_EXAMPLE>
   <USER_EXAMPLE>
   <INTERNAL_ID_EXAMPLE>
   ```

7. Do not preserve identifying data through hashes, encodings, truncated
   values, initials, stable pseudonyms derived from source values, or reversible
   substitutions.
8. Exact counts, rare labels, filenames, line numbers, or combinations of
   structural facts may themselves identify an environment. Generalize them
   when they are not essential to parser design.
9. The output is not approved for public transfer merely because you created it
   or completed the self-check. Human structural and security reviews are still
   mandatory.

The prohibition on real values applies to this structural report because it may
later cross into public development. It does not imply that future local
corporate remediation reports must hide every ordinary non-secret value.
Secrets and equivalent sensitive values must nevertheless remain redacted by
default in all durable reports.

## Analysis method

Work from structural observations across both files. You may use local
read-only inspection and temporary computations, but do not persist raw excerpts
or derived value inventories. Prefer aggregate structural observations and
synthetic examples.

For every conclusion:

- distinguish observed facts from recommendations;
- state whether it applies to PREPROD, NT, or both without exposing identifying
  details;
- record uncertainty explicitly;
- do not infer semantics from ordering, indentation, naming, or repetition
  unless the evidence supports that conclusion;
- identify what must be validated later against real configs in the corporate
  environment.

If the two sources do not provide enough evidence to establish a rule, mark it
`UNKNOWN` or `REQUIRES CORPORATE VALIDATION`. Do not invent syntax, canonical
paths, precedence, or override behavior.

## Required analysis questions

Analyze and safely document all of the following.

### 1. Actual source format

- File encoding, newline convention, and overall serialization style.
- Whether the format is formally specified, semi-structured, or custom.
- Delimiters, escaping, quoting, comments, continuation rules, and terminators.
- Whether unrelated formats or generated wrapper content are mixed together.
- How malformed or partial content can be detected without guessing.

### 2. Block structure

- How blocks begin and end and whether they can nest.
- Which structural elements identify block kind and scope.
- Whether block boundaries are explicit or inferred.
- Whether equivalent blocks can appear in different physical positions.
- A synthetic grammar or state-machine sketch when supported by evidence.

### 3. Canonical identity

- Which structural fields jointly identify a logical configuration record.
- Whether identity is scoped by block, hierarchy, environment, realm, service,
  or another observed structural dimension.
- Which parts of identity require normalization and which must remain exact.
- Cases where a canonical identity cannot be established deterministically.
- Collision risks and how they should be surfaced rather than silently merged.

### 4. Parameters, multiline values, and multi-value values

- Parameter declaration forms and separators.
- Empty, null, missing, boolean, numeric, and textual representations.
- Continuation and multiline rules.
- Repeated parameter syntax and collection boundaries.
- Whether multi-value ordering is significant, insignificant, or unknown.
- Escaping and normalization constraints that affect equality.

### 5. Environment variables and substitutions

- How variable definitions and references are represented.
- Scope, expansion timing, defaults, and unresolved-reference behavior when
  observable.
- Whether comparison should use literal expressions, resolved values, or both.
- Cycles, nested substitutions, and failure cases.

### 6. Duplicates, precedence, and overrides

- Whether duplicate identities or parameter declarations occur.
- Whether duplicates are errors, ordered overrides, merges, collections, or
  context-dependent.
- Evidence for precedence rules across blocks or files.
- Which semantics remain unknown and must block deterministic consolidation.

### 7. Ordering semantics

- Which order is presentation-only and which order affects meaning.
- Whether records, blocks, or multi-values may be safely sorted.
- Cases where moving an item would change effective configuration.

### 8. Environment-specific values

- Structural categories likely to vary legitimately between PREPROD and NT.
- How environment-specific values can be classified without embedding examples
  from the real sources.
- Which differences should remain actionable and which may be governed by an
  explicit comparison policy.

### 9. Sensitive values

- Structural indicators that a value is a password, secret, token, private key,
  certificate material, credential, or equivalent sensitive content.
- Cases where classification is uncertain and must fail closed.
- Redaction requirements for `result.json`, HTML, CSV, logs, diagnostics, and
  resolver payloads.
- Distinguish secrets from ordinary non-secret configuration values that a local
  corporate remediation report may need to display.

### 10. Deterministic parser feasibility

- Whether a deterministic parser is feasible for the observed format.
- Recommended parsing strategy: grammar, tokenizer/state machine, line-oriented
  parser, structured-library parser, or another evidence-based approach.
- Streaming or incremental-processing opportunities for multi-megabyte inputs.
- Recovery behavior for malformed or unsupported constructs.
- Parser-version and source-format-version information that results should
  retain.

### 11. Recommended conceptual ConfigRecord

Recommend the smallest evidence-based conceptual record contract. Describe
fields by meaning, not Python class names. Consider:

- source role;
- canonical identity components;
- logical grouping;
- raw-versus-normalized comparison representation without exposing values;
- value kind and collection semantics;
- safe provenance;
- sensitivity classification;
- ordering or precedence metadata;
- parser and normalization policy versions;
- unresolved or unsupported state.

Mark every field as required, optional, or unresolved, and explain the evidence
for it. Do not freeze fields that the observed structure does not justify.

### 12. Deterministic matching strategy

- Exact identity rules and normalization prerequisites.
- Separation of record correspondence (`match_method`) from value comparison
  (`value_status`).
- Missing-in-NT, unmatched-NT, duplicate, collision, and ambiguous outcomes.
- Candidate-generation rules that do not depend on physical order.
- Stable result ordering used only for reproducible output.
- Situations that must remain `AMBIGUOUS` rather than being guessed.

### 13. LLM-assisted cases

- Identify only ambiguous cases where deterministic processing cannot decide.
- Define the minimum record/candidate subset and sanitized context a resolver
  would need; never propose sending either complete configuration.
- Describe how semantic decisions remain distinguishable from deterministic
  matches.
- Do not use resolver self-reported confidence as acceptance authority.
- Recommend inputs to a configured deterministic acceptance policy. If a
  resolver output does not satisfy that policy, the result remains
  `AMBIGUOUS`.
- State whether an LLM is unnecessary for the observed data.

### 14. Report grouping and remediation usability

- Evidence-based logical grouping suitable for HTML and CSV reports.
- Stable labels that can be generated without exposing internal identifiers.
- Useful filters and actionable categories.
- Which permitted non-secret values a local corporate reviewer may need for
  remediation and which fields must always remain redacted.

### 15. Risks and unknowns

- Parsing ambiguity, format drift, hidden precedence, lossy normalization,
  identity collisions, sensitive-data leakage, performance risks, and any
  environment-specific limitations.
- For each risk, state mitigation and required corporate validation.
- Maintain a separate list of unresolved questions; do not disguise them as
  recommendations.

### 16. Recommended architecture

Evaluate and refine this intended flow based on observed structure:

```text
configuration sources
-> deterministic parser
-> canonical records
-> normalization
-> deterministic matching
-> candidate generation
-> optional resolver for ambiguous cases only
-> configured deterministic acceptance policy
-> policy-compliant result.json
-> standalone HTML and CSV reports
```

Recommend changes only when supported by structural evidence. Keep processing
local, explainable, testable, and free of mandatory external SaaS.

## Required report structure

Create `analysis/openam-config-structure.md` with these sections:

1. `# Sanitized OpenAM Configuration Structural Analysis`
2. `## Scope and safety statement`
3. `## Executive summary`
4. `## Evidence and confidence boundaries`
5. `## Source format`
6. `## Block structure`
7. `## Canonical identity`
8. `## Parameters and value forms`
9. `## Environment variables and substitutions`
10. `## Duplicates, precedence, and overrides`
11. `## Ordering semantics`
12. `## Environment-specific values`
13. `## Sensitive-value classification`
14. `## Deterministic parser feasibility`
15. `## Recommended conceptual ConfigRecord`
16. `## Deterministic matching strategy`
17. `## LLM-assisted ambiguity boundary`
18. `## Report grouping and remediation needs`
19. `## Risks and mitigations`
20. `## Recommended architecture`
21. `## Unresolved questions and required corporate validation`
22. `## Sanitization self-check`

Use synthetic examples only. Clearly label every statement as `OBSERVED`,
`INFERRED`, `RECOMMENDED`, `UNKNOWN`, or `REQUIRES CORPORATE VALIDATION` where
that distinction affects implementation.

## Mandatory sanitization self-check

Before declaring the report ready for human review:

1. Re-read the entire report, including headings, tables, code blocks, metadata,
   and generated diagnostics.
2. Check that it contains no real source filename or path and no copied source
   excerpt.
3. Check for real hostnames, IP addresses, URLs, DNs, usernames, email-like
   identifiers, environment identifiers, credentials, passwords, secrets,
   tokens, certificates, private keys, internal IDs, hashes derived from real
   values, or reversible encodings.
4. Replace every questionable example with a visibly synthetic placeholder.
5. Confirm that structural combinations and exact counts do not identify the
   organization or environment.
6. Confirm that neither source configuration was modified.
7. Run `git status` only as a read-only check and confirm that nothing was
   staged or committed. Do not run `git add`, `git commit`, `git push`, or any PR
   creation command.
8. Record the self-check in the final section using this exact form:

   ```text
   Source files modified: NO
   Real corporate values present: NO
   Secrets or credentials present: NO
   Real infrastructure identifiers present: NO
   Files staged or committed: NO
   Automatic push or PR created: NO
   Sanitization self-check: PASS | FAIL
   Ready for manual/security review: YES | NO
   ```

If any item fails or is uncertain, set `Sanitization self-check: FAIL` and
`Ready for manual/security review: NO`. Keep the report local, explain the issue
without reproducing the sensitive content, and wait for operator guidance.

Do not claim that the report is approved for public transfer. Only an explicit
human approval after manual structural and security review can authorize that
transfer.
