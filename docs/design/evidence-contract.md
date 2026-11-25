# Shared execution and evidence contract

Scope update, 2026-11-12: this is the later chain-validation/assessment contract. The selected
read-only companion uses [companion-result/v1](companion.md#4-workflow-and-execution-contract)
under DR-008. Both preserve execution/provenance meanings; an expiry observation is not a
cryptographic-valid verdict. Neither result API is implemented yet.

Status: proposed M0 contract, 2026-11-11; implementation and acceptance pending.
Decision: [DR-007](../decisions/DR-007-first-shared-journey.md). Requirements: R-03, R-04,
R-08, R-09, R-10. This is the contract referenced by all three designs, not an implemented API.

## One observation, separate interpretations

Each result has a schema version and stable result/check IDs within a recorded run. Preserve:

| Field | Meaning |
|---|---|
| `execution.status` | `completed`, `error` or `not_run`; infrastructure/input errors are not certificate findings |
| `execution.exit_code` | The actual process status, null if no process ran |
| `observation.outcome` | `valid`, `invalid` or `unknown` for this check; never inferred from any nonzero exit alone |
| `observation.reason` | Stable normalized reason, with the raw diagnostic retained as evidence |
| `assertion.status` | `met`, `unmet` or `not_evaluated`, against an explicit expected outcome/reason |
| `provenance` | Exact identities and verification context below |
| `citations` | Page, section, option when applicable, source commit, content hash and source range |

An expected invalid chain can have `execution.status=completed`, `observation.outcome=invalid`
and `assertion.status=met`. A missing input file or a container startup failure must not satisfy
an expected certificate-validation failure. No product treats a generic `status=fail` as all
three of these meanings.

The workshop adds assessment and feedback for the learner's output. The terminal maps the
observed result to an operator finding. Neither duplicates the core verification command.
Reusable checks live in core; lessons reference rule IDs and bind artifact paths. Arbitrary
inline verification commands in lessons are outside the first contract.

## Reproducibility and explanation

Record the OpenSSL implementation, exact release/build, image digest and platform, rule revision,
documentation commit and section hash, input hashes, explicit trust inputs, verification time,
provider identities and configuration identity. Record absent/default configuration explicitly.
Do not store private key bytes or secret environment values in the provenance record.

Aliases such as `3.5` resolve to a recorded exact version before execution. An example tag is
not a digest. A result without the required context is incomplete evidence, not a reproducible
finding. The version support matrix must cite the official release lifecycle separately from
the available Git branches/tags.

An explanation attached to a finding uses that finding's execution/documentation context, even
when the host has another release. Standalone explanation requires an explicit release or an
explicitly selected host probe context. Unknown or unsupported implementations are reported;
do not substitute a nearby OpenSSL installation for evidence about LibreSSL or another stack.

## Artifact and sandbox boundary

M0 uses locally reviewed core rules and disposable fixtures. Input mounts are read-only; learner
work uses a separate writable directory. Default execution has no network, no host home or
container socket, no privileged mode, and no undeclared mounts. The runner sets a time limit,
resource/output limits, a non-root execution identity where supported, and cleanup on failure.
Exact limits and runtime support must be established by the M0 spike before a runner is accepted.

Do not persist raw private keys, credentials, command secrets or unrestricted logs by default.
Reject or safely classify unsupported sensitive input at M0. Deliberate export of sensitive
material needs a later reviewed contract; UI masking alone is insufficient. External unreviewed
rules are not declared safe because they run in a container.

## Documentation changes

Released source snapshots are immutable by commit/content identity. If a tag moves, flag the
identity mismatch rather than replacing an old snapshot silently. The first drift feed compares
recorded release snapshots and identifies changed, removed and unparsable sections. Changes on
a moving development branch are a separate, unimplemented monitoring stream.

The workshop maps those section identities to dependent lessons, re-runs affected checks and
marks explanatory content for review even if commands still pass. A missing section or parsing
failure is visible. A controlled drift fixture must select an affected lesson and leave an
unrelated lesson alone; label the fixture demonstration as simulated.

## Acceptance before either product claims M0

- A complete chain verifies using only the explicit trusted root and supplied intermediate.
- An incomplete chain produces the expected validation reason, not merely a nonzero status.
- Missing input, unrelated command failure and container failure cannot pass the negative assertion.
- An explanation remains tied to the result when the host release differs.
- Both consumers parse the same versioned result and reject unsupported schema versions.
- Sensitive fixture bytes do not appear in default display, logs or persisted result files.

The fixture tests, consumer API and exact limits remain M0 deliverables. This document does not
claim they exist or passed.
