# Corporate structural-analysis handoff

This directory contains the public, synthetic-only instructions for analyzing
real OpenAM configuration sources inside an approved corporate environment. It
must not contain source configurations, extracted values, generated corporate
reports, or other real corporate data.

## Workflow

```text
public repository
        |
        v
clone or pull into the corporate environment
        |
        v
Kilo reads kilo-structural-analysis-prompt.md
        |
        v
Kilo performs read-only analysis of real PREPROD and NT configs
        |
        v
Kilo creates local analysis/openam-config-structure.md
        |
        v
manual structural review + security review
        |
        v
only after explicit approval, the sanitized report may be transferred
back into public development
```

The Kilo operator supplies the real PREPROD and NT source paths locally. Those
paths, filenames, and contents are not copied into this repository or into the
sanitized report.

## Mandatory controls

- Kilo treats both source configurations as read-only.
- Kilo writes only the local structural report at
  `analysis/openam-config-structure.md` unless the operator explicitly chooses a
  different safe local working copy.
- Kilo must not stage, commit, push, open a pull request, upload, or otherwise
  publish the structural report automatically.
- The report must use synthetic placeholders and must not contain real config
  values, hostnames, IP addresses, URLs, DNs, usernames, credentials, secrets,
  tokens, certificates, private keys, or internal identifiers.
- Kilo performs the prompt's sanitization self-check before handing the local
  report to a human reviewer.
- A human structural review and security review are mandatory. Report transfer
  back to public development requires explicit approval after both reviews.

The local report is not approved for publication merely because it was
generated successfully or passes Kilo's self-check. Automatic commit or push of
`analysis/openam-config-structure.md` is prohibited.

This structural-analysis report has a stricter boundary than future local
corporate comparison reports: remediation reports may contain policy-approved
non-secret values inside the corporate environment, but the structural report
is intended for possible public-development transfer and therefore contains no
real corporate values at all.
