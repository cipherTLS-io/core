# DR-009 — Move to the CipherTLS organization and rename core and client

Date: 2026-11-24. Owner: veronaimars.
Status: decided by maintainer; supersedes repository, Go module and CLI naming clauses in DR-002 and DR-003.

## Question

What organization, repository and CLI names represent the program as a clean-room cryptography,
TLS and AI-agent initiative alongside OpenSSL?

## Context

- The earlier working title ("the cryptographer's terminal") did not resonate with prospective
  users and suggested an academic grading simulator rather than a real OpenSSL companion and
  operator tool.
- The program lives under one GitHub organization (`ciphertls-io`) following the multi-repository
  organization layout of the reference baseline.
- Third-party tools should avoid using the OpenSSL trademark in their primary product name without
  written permission while keeping nominative references ("for OpenSSL") clear.

## Decision

- Organization: **`ciphertls-io`** (umbrella initiative: **CipherTLS**).
- Repositories and Go modules:
  - `github.com/ciphertls-io/core` (was `VeronAImars/cryptographers-core`), CLI binary `ciphertls-core`.
  - `github.com/ciphertls-io/aitls` (was `VeronAImars/cryptographers-terminal`), CLI binary `aitls`.
  - `github.com/ciphertls-io/openssl-workshop` (was `VeronAImars/openssl-workshop`), CLI binary `workshop`.
- Cross-repository phase dependencies in `docs/PHASES.md` reference `core:<phase>` inside the
  `ciphertls-io` organization.
- Future experimental or provider work (such as an OpenSSL 3.x/4.x provider or agent custody
  layer) may land under `ciphertls-io` only through its own design and decision record; this record
  does not authorize forking upstream OpenSSL.

## Effect on prior records

- Supersedes the repository names, module paths and CLI names in DR-002 and DR-003.
- Preserves DR-001 (clean-room open source), DR-003's three-repository boundary and public PRD
  placement in `core`, DR-004 through DR-008, and all existing requirement and phase IDs (`R-01`–`R-18`,
  `M0`–`M7`).
- Historical decision records DR-002 and DR-003 retain their original text untouched.
