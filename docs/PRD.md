# PRD — CipherTLS (`core`, `aitls`, `openssl-workshop`)

| | |
|---|---|
| Maintainer and requirements owner | veronaimars |
| Status | companion-first direction selected; M6.0 spike in progress; technical targets unproved |
| Version · date | v0.4 · 2026-11-24 |
| Existing decisions | DR-001 independence; DR-002 language and shape; DR-003 repository boundaries and PRD placement; DR-004 retired-entry-point signpost; DR-008 companion-first delivery; DR-009 CipherTLS org (`ciphertls-io`) and rename |
| Scope | Grounded OpenSSL companion first (`M6`), bounded NL evaluation (`M7`), later inspection/workshop (`M0`+); public requirements across `ciphertls-io/{core,aitls,openssl-workshop}` |

This document defines the intended outcome. The [program map](program.md) identifies the owning
records. Public requirements stay here; personal conference preparation and outreach stay in
private planning. No sponsor relationship or endorsement is implied.

## 1. Problem and exposure

**Unified mission:** CipherTLS makes real OpenSSL verifiable, version-accurate, and safe for both
humans and AI agents — without hiding the binary, hallucinating flags, or handing secrets to a
model.

Why not just a plain terminal with a bag of tools (`openssl`, `step`, `man`) or a raw LLM in a
shell? For an expert running a memorized one-off command, direct `openssl` is already faster. The
plain-terminal baseline breaks down on three recurring failure modes:

1. **Stack conflation:** On macOS and mixed hosts, `/usr/bin/openssl` (LibreSSL), Homebrew OpenSSL,
   Python, Node, and `curl` routinely disagree; installing another Go/Rust CLI adds another verifier
   rather than explaining the OpenSSL binary a system actually runs.
2. **Silent manual and runbook drift:** Downstream docs, student labs, and LLM prompts routinely
   cite EOL OpenSSL 1.1.1 flags or removed 3.x options against OpenSSL 4.x binaries.
3. **Unbounded AI execution:** Letting an LLM type raw `openssl` shell commands hallucinates flags,
   conflates exit-code-0 inspection with cryptographic trust, and leaks private keys into context.

Deterministic grounding (`M6`) comes first because it is the guardrail that makes AI safe for
cryptography (`aitls`). Its total upkeep must still be compared honestly against direct OpenSSL and
manuals/browser (`R-18`); a feature list does not establish demand or maintenance savings. See
[DR-008](decisions/DR-008-companion-first.md) and [DR-009](decisions/DR-009-ciphertls-org-and-rename.md).
The chain-inspection and learning motivation remains later scope (`M0`+), not a prerequisite for
proving the companion foundation.

Learning material and operator explanations can drift from the OpenSSL release actually used.
An apparently correct command may use the wrong trust inputs, and a correct explanation may
refer to a different release. The intended product connects real artifact checks to the exact
manual and execution context, then presents that evidence for a learner or an operator.

The earlier draft reported problems in the maintainer's previous teaching material. Those are
historical motivation, not acceptance evidence for the new implementation. Current proof comes
from named fixtures and reproducible runs.

## 2. Goals and non-goals

Goals
- G1 Provide a current learning path with observable learner-created output and release-specific
  feedback, reachable from the retired teaching entry point.
- G2 Make changes between cited release snapshots observable and route affected material to
  revalidation and content review.
- G3 Let an operator inspect an artifact and see the check, context and source behind a finding.
- G4 Share verification and source knowledge across both products.
- G5 Reduce repeated lookup/update effort without hiding the selected executable, source identity
  or incomplete evidence. Compare the companion with the simpler baseline before expanding.

Non-goals for the first delivery
- A chatbot whose answer substitutes for a check.
- Reimplementing cryptographic verification already provided by OpenSSL.
- Broad policy coverage, generated courses, a browser sandbox or a complete TUI before the first
  companion task works.
- A paid or hybrid edition.

## 3. Actors

| Id | Actor | Kind |
|---|---|---|
| A1 | Learner with shell familiarity, learning PKI | consumer |
| A2 | Operator inspecting cryptographic artifacts | consumer |
| A3 | Lesson author | contributor |
| A4 | Maintainer | operator |
| A5 | Upstream OpenSSL project, source of manuals and binaries | external; no endorsement presumed |

## 4. Requirements

Stable IDs from v0.1 are retained. R-01–R-11 continue to govern the later chain/learning work.
R-12–R-18 define the selected companion pilot. These are acceptance targets, not implemented behavior.

| R | Required outcome | For | Delivery / acceptance |
|---|---|---|---|
| R-01 | Source material is preserved unmodified with exact release and commit identity, and citations resolve to it | A3, A4 | core M0: snapshot and section-hash evidence |
| R-02 | Users can identify changed command options between two recorded release snapshots | A1, A2 | core M0: known additions/removals on a named fixture |
| R-03 | Artifact checks run the intended real OpenSSL with reproducible context; expected cryptographic failure differs from input/runtime failure | A1, A2 | core M0: positive, intended-negative and error cases; shared consumer contract |
| R-04 | Author validation exercises every declared exact release; learner assessment reads the learner's submission without applying the reference solution | A1, A3 | workshop M0: untouched/wrong submissions fail and correct alternatives pass |
| R-05 | Published command/output examples identify the actual captured run and release | A1 | workshop M0: page and run record agree |
| R-06 | The retired teaching entry point leads readers to the published replacement | A1 | workshop M0: published replacement and retired-repository link, per DR-004; no HTTP 301 requirement |
| R-07 | Changes to cited release snapshots select affected lessons for re-run and content review, with deduplicated failures | A3, A4 | workshop M2 + core M1: controlled affected/unaffected fixture; night-run target starts when a new snapshot is detected |
| R-08 | Inspection reports parsed artifact fields and findings from shared checks | A2 | `aitls` M0: complete/incomplete chain and explicit trust context |
| R-09 | Explanations cite the execution/source context of the finding; standalone queries explicitly select a release/context | A1, A2 | `aitls` M0; workshop M4: differing host release cannot change an attached citation |
| R-10 | Sensitive artifact material is absent from default display, logs and persisted results from the first artifact-reading phase | A2 | `aitls` M0 and `core` M0: sensitive fixtures; M2 extends the checks to panes/cases |
| R-11 | Model-generated material remains a draft until technical validation and a person's content review | A3, A4 | generation/publication phases: review and validation evidence |
| R-12 | A small reference pack resolves the selected implementation/build to immutable source identity; unknown mappings never fall back to latest | A2, A4 | `core` M6: correct/mismatched/unsupported fixtures |
| R-13 | Cached references work offline; failed refresh preserves last successful bytes and exposes age/error separately | A2, A4 | `core` M6: cached, missing-cache, unchanged, changed and failed-refresh cases |
| R-14 | Explicit task selection reports certificate expiry through the chosen existing OpenSSL with typed inputs and bounded direct argv execution | A2 | `core`/`aitls` M6: known/expired/malformed/missing inputs, process error and literal paths; no trust verdict inferred |
| R-15 | NL is an optional task selector; missing/ambiguous/unsupported requests cannot cause guessed execution; the base install works without a model | A2 | `core`/`aitls` M7: held-out language cases, deterministic baseline and no-NL installation |
| R-16 | Reference changes identify affected consumers; executable changes also trigger checks; new explanatory/expected behavior is reviewed separately from fetching | A3, A4 | `core` M6: related/unrelated changes and a runtime change with unchanged prose |
| R-17 | First-use docs, compatible core/pack pins and a clean-install preview reproduce the chosen task and accurately state limitations | A2 | `aitls` M6: captured first-use output and exercised installation; no scaffold-as-release claim |
| R-18 | Total setup, repeated task and update-review effort is recorded against direct OpenSSL plus manuals/browser, including helper upkeep | A4 | `aitls` M6/M7: dated pilot record, agreed criteria and a continue/simplify decision; no invented savings |

## 5. Success metrics

- First companion: expiry output and reference match the selected context; unknowns/errors remain
  explicit. Record baseline effort and the companion's upkeep (G5); no saving is yet measured.
- Later shared journey: correct repair passes, untouched and wrong submissions fail, and the terminal
  reports the same underlying observation with the same source context (G1, G3).
- Reproducibility: a clean environment reproduces the named fixture outcomes using recorded
  dependencies, images, inputs and time/trust context (G1, G3).
- Drift: the controlled source-change case selects the affected lesson, leaves an unrelated one
  alone and requests content review even when the command still passes (G2).
- Shared knowledge: consumer contract tests demonstrate both products use the core result and
  rule identity; duplicated verification logic is a review defect (G4).
- Learning: an observed pilot records completion, help needed, misconceptions and performance on
  a different artifact. A passing automated exercise alone does not establish learning.

## 6. Constraints and assumptions

- Verified source, 2026-11-11: the [official downloads page](https://openssl-library.org/source/)
  distinguishes supported releases from older versions. A branch or tag inventory is not a
  support matrix. Resolve exact tested releases and record the matrix with each delivered build.
- Public sources: [OpenSSL source](https://github.com/openssl/openssl) and
  [documentation build](https://github.com/openssl/openssl-docs). Preserve applicable notices.
- Existing decisions: independent work and Apache-2.0 under DR-001; implementation shape under
  DR-002; repository ownership and public PRD under DR-003.
- Assumptions to test: selected host builds can be mapped reliably; the later workshop audience
  can use the selected container environment. A host inspection is not a service-level diagnosis.
- Current state: scaffolds and design records; no working product release or completed product milestone.

## 7. Open decisions

DR-004 is resolved by the [retired-entry-point record](decisions/DR-004-retired-entry-point.md),
which preserves the maintainer's 2026-11-08 signpost decision. The earlier 301 requirement was stale.

| DR | Question | Owner | Status |
|---|---|---|---|
| DR-005 | Where does the future shell tutor belong? | veronaimars | open; workshop remains the proposed location |
| DR-006 | Whether and how to approach upstream about the work | veronaimars | private coordination; no outreach or endorsement claimed here |
| DR-007 | What first shared journey and evidence contract should the products implement? | veronaimars | proposed later chain contract; first-delivery priority superseded by DR-008; M0 evidence pending |
| DR-008 | Companion first, optional NL and replaceable source adapter | veronaimars | delivery direction selected 2026-11-12; technical evidence pending; supersedes chain-first priority |

## 8. Dependencies and risks

- Terminal M6 depends on core M6; M7 is optional. Later chain consumers independently depend on
  core M0. No terminal-to-workshop dependency or mandatory external source service is introduced.
- Source identity, execution errors and cryptographic outcomes can be confused; the draft
  [evidence contract](design/evidence-contract.md) defines distinct states and negative cases.
- Fixture self-validation can masquerade as learner achievement; R-04 separates the two paths.
- Private-key masking can hide display while logs leak; R-10 starts at artifact ingestion.
- Documents can agree while the product does not work; acceptance evidence is required before
  availability claims or completion states change.

## 9. Out of scope (first usable delivery)

Broad TUI, protocol explorer, inventory, notebook, named policy sets, model-generated courses,
remote practice targets and a browser sandbox. These remain roadmap direction, not prerequisites
for proving the first companion task. They remain later possibilities, not release commitments.

## 10. Approval

| Role | Handle | Date / state |
|---|---|---|
| Requirements owner | veronaimars | companion-first setup selected 2026-11-12; detailed targets remain unproved and subject to implementation review |

## Annex A — Basis of stated facts

| Claim | Label | Basis |
|---|---|---|
| Support differs from branch/tag presence | verified public source | Official downloads page, checked 2026-11-11 |
| The three projects are scaffolds | verified checkout | Go entry points, pending M0 scripts, phase ledgers and setup reviews |
| The former contracts conflict | verified document inspection | DR-007 identifies the conflicting design/phase statements |
| Later chain journey is a useful learning/inspection boundary | proposed | Requested rework and the two products' common dependency; not user-research evidence |

## Change log

| Version | Date | Change |
|---|---|---|
| v0.1 | 2026-11-07 | Initial problem, actors and R-01–R-11 mapping; retained in Git history |
| v0.2 | 2026-11-11 | Preserve IDs; clarify assessment, provenance, release-context explanation, early sensitive-input handling and draft status; separate public contracts from private coordination |
| v0.3 | 2026-11-12 | Add R-12–R-18 and DR-008 for companion-first delivery; retain earlier IDs and separate technical acceptance from maintenance/user-value evidence |
