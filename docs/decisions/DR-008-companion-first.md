# DR-008 — Companion first, optional natural-language package

Date: 2026-11-12. Owner: veronaimars.
Status: delivery direction selected by maintainer; implementation contracts and acceptance remain unproved.

## Question and basis

What should be set up first when maintenance and accuracy are the primary constraints?
The maintainer selected a documentation companion plus a small read-only natural-language
experiment. No demand study, measured maintenance saving or model performance is claimed.

## Decision

- Keep the existing three repositories. Core owns reference packs, version/context resolution,
  the command bridge and workflow definitions. An optional NL adapter is maintained here with
  separate packaging; ordinary core use must not require a model/runtime.
- The terminal repository supplies the companion CLI first. The first task is
  `certificate.expiry`: inspect a supplied PEM certificate's notAfter value with the selected
  existing OpenSSL binary and attach the applicable reference. This is not certificate validation.
- A small source adapter refreshes declared references and preserves immutable identities.
  Its contract permits a later source-service replacement without changing task semantics.
  No external source daemon is required to install or use the first companion.
- NL selects a supported task and proposes typed inputs. The deterministic bridge validates
  these and builds the argument vector. Current documentation and commands stay outside model
  weights. The model implementation remains unselected; compare against ordinary task selection.
- Workshop lessons, chain validation in pinned images, large corpora and the full workbench remain
  later scope. Either consumer operates independently; the workshop is not a terminal dependency.

## Effect on prior records

This supersedes DR-003's build-order consequence that terminal needs workshop, and the
container-only scope of DR-002 for the new read-only companion task. Repository ownership,
public PRD placement, Go implementation direction and controlled workshop grading remain.
DR-007's chain-first proposal is no longer the first-delivery plan. Its distinction among process
errors, cryptographic outcomes and assessment remains relevant to the later chain work; its
unproven technical contract is not declared accepted by this delivery decision.

Preserve prior phase and requirement IDs. Core/terminal M6 is the companion pilot; M7 is the
optional NL experiment. Earlier IDs remain unfinished and later in priority. M0-M5 already have
historical or roadmap meanings; numbers are stable identities, not execution order. Dependencies
and `current_phase` state the selected order. No gate is marked complete by this decision.

## Acceptance and boundaries

The [companion design](../design/companion.md) defines R-12–R-18 and M6/M7 targets. Before a release,
demonstrate the task, source matching, offline/error behavior, safe argument handling and a clean
installation. Compare total setup, repeated lookup and update effort with direct OpenSSL plus
manuals/browser. Training, public release, repository visibility and upstream outreach are not
implied by this setup decision.
