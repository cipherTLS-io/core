# DR-007 — First shared learning and inspection journey

Date: 2026-11-11. Status: proposed; maintainer review pending. System owner: veronaimars.

## Question

What contract must core and both consumers prove before expanding corpus, lessons or UI?

## Evidence

Verified from the pre-rework designs and ledgers: products require core but their M0 dependency
lists are empty; core M2 says consumers stop shelling out although DR-003 specifies imports;
the finding example conflates unsuccessful verification with successful negative assertion;
the sample lesson creates the same artifacts it checks; terminal explanations select the host
release independently of the check's image. These are design inconsistencies, not measured
failures of an implemented runner.

## Options and cost of being wrong

| Option | Cost of being wrong |
|---|---|
| Implement each existing outline independently | Duplicate checks and incompatible consumers; apparent lesson completion without learner action |
| Define a narrow shared contract and prove it in both products | More explicit M0 work; provisional API may change together before release |
| Wait for full corpus and stable API before consumer work | Delays discovering whether the central teaching/inspection path is useful |

## Proposed resolution

Use the [evidence contract](../design/evidence-contract.md). Core M0 exposes a narrow provisional
Go interface; M2 stabilizes it. Both consumers pin a reviewed revision and then a release.
Assessment reads a learner-created artifact; infrastructure errors never satisfy a negative
cryptographic assertion. Explain uses the result's source context. Key handling starts at M0.

Update the existing M0 rows rather than renumbering them or marking documentation as product
completion. The shared path is a prerequisite to claiming replication of the k3sm baseline.
DR-003 repository ownership and public PRD placement remain in force.
Its build-order consequence is clarified: the terminal consumes core independently of the
workshop; it does not require the workshop to operate. This clarification is part of the proposal.

## Consultation and acceptance

No external consultation or implementation trial is claimed. This proposal comes from a review
of the checked-in contracts and the requested project rework. Maintainer decision and dated M0
spike evidence are pending. Record revisions here and in the design before implementation.
