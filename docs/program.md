# OpenSSL companion, learning and inspection — program map

Status: design and scaffolds; no product milestone has been demonstrated. Maintainer: veronaimars.

Selected direction, 2026-11-12: documentation companion first, then one optional NL experiment.
The first task reports a certificate's expiry using the selected existing OpenSSL and a matching
reference. Maintenance savings are a hypothesis to test against direct OpenSSL plus manuals.
[DR-008](decisions/DR-008-companion-first.md) records this direction; the
[companion design](design/companion.md) defines package boundaries and acceptance targets.

The later chain journey remains: a learner repairs an artifact; an operator inspects it; both
use the same verification evidence. The workshop and full workbench are optional later scope.

## Ownership and records

| Responsibility | Authority |
|---|---|
| Public product requirements across the program | [PRD](PRD.md), with stable R-01–R-18 IDs; implementation acceptance remains unproved |
| Shared execution, evidence and citation contract | [Evidence contract](design/evidence-contract.md) and [core design](DESIGN.md) |
| Companion reference/bridge/NL contracts | [Companion design](design/companion.md), R-12–R-18; execution and classification remain separate |
| Learning objective, learner output and assessment | [Workshop design](https://github.com/ciphertls-io/openssl-workshop/blob/main/docs/DESIGN.md) |
| Inspection, findings and explanation presentation | Terminal's own design; access follows its repository settings |
| Implementation state | Each repository's `docs/PHASES.md`; no second program completion ledger |
| Technical choices | Owning repository's `docs/decisions/`; cross-product choices originate in core |
| Review and merge procedure | Each repository's `AGENTS.md`, `CONTRIBUTING.md` and executable gate |
| Baseline comparison | [Setup review](setup-review.md), with companion reviews in the products |

The core depends on neither product. Workshop and terminal depend independently on core.
The companion bridge is provisional in core M6; the later chain interface remains M0/M2 work.
Consumers pin an actual reviewed core revision, then a release when one exists. A local
development replacement is not a published dependency or compatibility claim. NL and an external
source service are optional; ordinary explicit task selection must work without either.

## First delivery and what follows

1. Core M6: a small immutable reference pack, replaceable source adapter, selected binary and
   read-only expiry task; prove exact provenance, error handling, offline behavior and refresh.
2. Terminal M6: reference lookup, explicit task selection, plan/result presentation and a tested
   clean installation/preview. Measure effort against direct OpenSSL plus manuals/browser.
3. Core/terminal M7: evaluate the optional NL package against that deterministic baseline.
   No model is selected or trained by setup. Do not expand if it adds upkeep without benefit.
4. Later: core M0 chain/sandbox contract, workshop M0 and terminal M0 independently exercising
   it, followed by broader learning/UI work when justified. Existing IDs are preserved.

These are implementation exits, not completion claims or calendar commitments.

## Package and repository map

| Existing repository | Selected package responsibility |
|---|---|
| Core | `pkg/reference`, `pkg/bridge`, optional `pkg/nl`; reference/workflow/NL packs under `packs/` |
| Terminal | Companion CLI first; operator workbench later; no duplicate manuals or verification rules |
| Workshop | Lesson packs, learner assessment and educational site; outside the first companion delivery |

These directories describe boundaries; no SDK or pack is published. A separate repository is not
required for each installable package. The public PRD stays here; no private program repo is added.

## Public and private documentation

Keep public product requirements, interface contracts, technical decision records, acceptance
criteria and evidence with the code. An external contributor must be able to understand a
requested change and reproduce its technical acceptance without access to private planning.
The existing PRD stays here under DR-003; its title does not make it confidential.

Private planning may contain personal conference preparation, outreach, unpublished priorities
and sensitive coordination. It may reference public requirement and phase IDs, but must not
duplicate their status or silently override them. A private constraint that affects public
behavior needs an appropriate public requirement or decision before implementation is judged
against it. Security disclosures follow `SECURITY.md` until disclosure is appropriate.

A separate private program repository is justified only by a real access or coordination need.
Changing DR-003's placement rule requires a new decision record and reference migration. Merely
having a PR/merge agent does not require another repository.

## Review agent contract

The agent reads the target repository's instructions, relevant R IDs, design, decisions, phase
IDs, gate manifest and actual diff. It verifies dependencies against the core revision the product
really consumes, runs the local gate, and records the command, revision, environment and verdict.
It distinguishes scaffold hygiene, unit tests, product acceptance and pending manual checks.

Private planning can guide priority and release coordination. Public review comments cite the
public contract and reproducible evidence. Missing private access is not a fabricated technical
failure. A conflict is reported to the maintainer with the affected requirement; an agent does
not settle it by moving a phase or changing a requirement silently. Running checks does not
itself grant authority to merge, publish or change access.
