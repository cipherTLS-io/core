# Local companion: references, execution and optional NL

Status: selected scope, 2026-11-12; technical acceptance targets, no implementation claim.
Authority: [DR-008](../decisions/DR-008-companion-first.md), [PRD R-12–R-18](../PRD.md).
Phases: core M6/M7; terminal M6/M7. The overall design links here from §5.6.

## 1. First user task and boundary

Given one disposable PEM certificate and an explicitly selected OpenSSL executable, show its
expiry date, the command used and the applicable source reference. The
[official x509 manual](https://docs.openssl.org/4.1/man1/openssl-x509/) describes `-enddate` as
printing notAfter (reported, checked 2026-11-12). This observation does not establish trust,
hostname validity, revocation or overall certificate validity.

Target argument vector: `x509`, `-in`, the bound certificate path, `-noout`, `-enddate`.
This is a design example, not a verified run. M6.0 establishes the exact supported build(s),
source mapping, output parsing and execution limits. Unlisted/unknown implementations fail
explicitly; do not substitute a nearby installation. The task describes the selected executable,
not what another service links. No network target, config mutation, key creation or repair at M6.

## 2. Package ownership and dependency direction

| Component | Proposed home | Responsibility |
|---|---|---|
| Reference pack and source adapter | `pkg/reference/`, `packs/reference/` | Known source retrieval, immutable snapshots, applicable-version resolution and freshness |
| Bridge SDK | `pkg/bridge/` | Typed task inputs, executable selection, argv construction, bounded execution and result |
| Workflow pack | `packs/workflows/` | Reviewed task definitions and their reference/test dependencies |
| Optional NL router | `pkg/nl/`, `packs/nl/` | Language to task selection; separate runtime/artifact, no compulsory model dependency |
| Consumer CLI | `aitls` `cmd/aitls/` | Reference lookup, explicit task selection, plan display and output presentation |

The directories are package boundaries for implementation, not importable APIs or published
artifacts today. Core is one Go module initially. A later SDK release requires a documented API
and real version pin. Both consumers depend on core independently; core imports neither consumer.
Do not create another repository for each package. Keep source-provider and NL dependencies
behind separate adapters so the base companion remains useful without either optional service.

### 2a. One-way doors vs. two-way doors

| Choice | Door | Why | Freeze point |
|---|---|---|---|
| Org & Go module paths (`github.com/ciphertls-io/*`) | **One-way** (after first public tag) | Go module proxy caches tagged module paths permanently. | Locked (`DR-009`). |
| `companion-result/v1` JSON schema (`execution`, `observation`, `provenance`, `reference_status`) | **One-way** (per major version) | Downstream consumers (`aitls`, scripts, future MCP) parse it; mixing process errors with trust verdicts breaks callers. | Freeze struct + golden fixtures in `M6.0`. |
| Immutable reference snapshot identity (source URL, commit SHA, raw SHA-256, section hash) | **One-way** | Citations and drift-triage keys depend on stable content-addressed identities. | Freeze canonical hash inputs in `M6.0`. |
| Source-fetch adapter (direct HTTP/git today vs. external source service later) | **Two-way** | Hidden behind narrow `FetchSource` / `ReadSnapshot` interface in `pkg/reference`. | Keep interface minimal; no private dependency. |
| Optional NL router backend (`M7`: rules vs. classifier vs. small local model) | **Two-way** | Hidden behind `pkg/nl` returning `{status, task_id, slots}` — never `argv`. | Defer selection until `M6` deterministic baseline is measured. |
| Pre-1.0 CLI flags and terminal formatting | **Two-way** | Pre-1.0 CLI ergonomics can evolve with changelog notes. | Keep `v0.1` flags minimal (`doc`, `expiry`, optional `ask`). |

## 3. Reference contract

Resolve a request by implementation, exact release/build context, page/section and channel.
Return source URL, immutable upstream commit where available, exact original content hash,
section locator/hash, source release, last successful upstream check and whether the selected
context is supported. Keep original bytes/notices beside any derived index.

Three channels are distinct: the snapshot attached to an execution; current candidate corrections
for its declared source line; information about a newer release. A branch can describe behavior
newer than the installed patch. A fresh fetch is not proof of applicability. Record unmatched or
vendor-modified builds as unsupported/unknown until the support matrix justifies the mapping.

`FetchSource` retrieves a declared source under byte/time limits and returns bytes plus identity;
`ReadSnapshot` reads an immutable local identity; `ResolveReference` establishes applicability.
These are conceptual operations, not implemented function names. Refresh never rewrites an old
run's reference or promotes an unreviewed task change. The implementation starts with a few known
files/pages; whole-site crawling, generic POD support and background daemons are not prerequisites.

Offline reads retain the last successful snapshot and expose age. A failed refresh records the
attempt/error separately and preserves the last successful content. A first run without a pack
reports missing references. A pinned read does not silently fall forward to newer content.
Network refresh is separate from task execution; public source fetches do not upload user inputs.

## 4. Workflow and execution contract

Task ID: `certificate.expiry`. Required input: one readable local regular file containing a PEM
certificate. Only this reviewed task is executable at M6. Reject unsupported key/config/URI inputs,
missing inputs and inputs over the selected limits. The spike determines exact limits before the
real gate replaces the skeleton; no unlimited input/output/time setting can pass M6.

Probe and record the selected executable's resolved identity and reported build before preparing
the plan. The plan contains the task/revision, input hash, exact argv, execution context, source
snapshot and limits. Invoke the selected binary directly without a shell; paths remain arguments.
Execution is an explicit caller action, separate from NL interpretation or plan preparation.
If input/binary identity changes between plan and execution, invalidate the plan and request a new
one. Reading from a stable disposable copy is acceptable if that identity is recorded.

Return a versioned `companion-result/v1` with `execution` (completed/error/not_run, exit status),
`observation` (certificate_expiry, raw notAfter and normalized UTC value only when recognized),
`provenance` (plan, binary/build, input, workflow and source identities), and `reference_status`.
An expiry observation has no cryptographic-valid verdict. Missing/malformed input, timeout,
unrecognized output and reference mismatch remain distinguishable. Preserve bounded diagnostics
without dumping certificate/private-key bytes or secret environment values into logs.

The [chain evidence contract](evidence-contract.md) retains its later validation/assessment scope.
Shared execution/provenance meanings must agree, but an expiry value must not be forced into its
`valid`/`invalid` assertion model. API schemas and compatibility tests are M6 implementation work.

## 5. NL contract and baseline

NL returns `status` = matched / clarify / unsupported, a permitted task ID and proposed typed
inputs. It cannot return executable argv, modify policy/limits or choose a new source. The bridge
validates every proposal again. Missing file selection and ambiguous/negated requests require
clarification; an unfamiliar request cannot become a guessed command. Reference search remains
available for unsupported tasks. Confidence is diagnostic, not an execution permission.

Start with ordinary task selection and reference lookup as the baseline. M7 compares an optional
router against it using held-out paraphrases, negations, missing inputs and out-of-scope requests.
Training data and evaluation cases are separate. Do not treat a one-task always-match classifier
as success. Choose rules, a classifier or a small local model based on measured benefit and upkeep;
no model training or dependency is selected by this document. Updating references should not
normally require retraining; changing task semantics can require recipes, evaluation and training.

## 6. Drift and maintenance proof

A reference section has explicit workflow/lesson consumers. On a change, produce the source diff,
affected IDs, recheck results and content-review status. Ignore unrelated-page changes when
selecting consumers. Runtime changes trigger checks independently of documentation changes.
Automatic retrieval does not automatically adopt new instructions or expected answers.

Compare direct OpenSSL plus manuals/browser against the companion on the same task and a
representative source update: initial setup, repeated lookup, correctness, update review, false
alerts and adapter repairs. Include upkeep of shared dependencies. Agree useful decision criteria
before the pilot; no maintenance savings or demand are established by setup or fixture tests.

## 7. Acceptance matrix (binding test rows)

Each row names the required Go test or e2e check that must fail before implementation and pass
before the gate flips `skeleton: false`. Pending rows are reported apart from failed rows.

| Case | Required result | Target test | Status |
|---|---|---|---|
| Valid PEM, listed build, matching snapshot | Correct notAfter value and exact command/context/reference | `TestM6_ExpiryValidPEM` | pending |
| Expired certificate | Date reported as an observation; never described as trusted/valid merely because inspection succeeded | `TestM6_ExpiryExpiredPEM` | pending |
| Missing/malformed/key input; process failure | Specific error, no fabricated date or validation finding, no key bytes leaked | `TestM6_ExpiryErrorAndKeyReject` | pending |
| Spaces/metacharacters in path | Literal argument handling; no shell execution or extra operation | `TestM6_ExpiryShellMetacharPath` | pending |
| Changed input/binary after plan | Stale plan rejected | `TestM6_ExpiryStalePlan` | pending |
| Cached reference offline / failed refresh | Same immutable content; last-success and failure/age visible | `TestM6_ReferenceOfflineAndFailedRefresh` | pending |
| Wrong release or unknown implementation | Explicit unsupported/mismatch; no fallback to latest | `TestM6_ReferenceUnsupportedBuild` | pending |
| Related / unrelated source change | Only declared dependents selected; runtime and explanation review reported separately | `TestM6_DriftSelectiveDependents` | pending |
| No NL package | Reference lookup and explicit expiry task still work | `TestM6_NoNLDependency` | pending |
| Ambiguous/unsupported NL | Clarification/unsupported, no command execution | `TestM7_NLAbstentionAndSafety` | pending |

M6 gates prove the deterministic journey and reference behavior; M7 gates prove the optional NL
integration and record the baseline comparison. A clean installation and user-facing preview are
`aitls` M6 deliverables. Full workshop/TUI acceptance remains in the existing later milestones.
