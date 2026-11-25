# DR-004 — Retired teaching entry point

Decision date: 2026-11-08. Recorded here: 2026-11-11. Status: decided.
System owner and decider: veronaimars. Basis: reported maintainer instruction, reconciled during
the documentation rework; no new redirect or hosting action was performed.

## Question

How should the retired teaching material direct readers to its replacement?

## Decision

The retired repository's README links to the replacement effort. Once the workshop page exists,
update that link to the published page. There is no HTTP 301 requirement: the prior hosted site
was retired. This corrects the obsolete 301 requirement in PRD v0.1.

## Options and consequences

Keeping a redirect endpoint would require control and operation of the old hosting endpoint.
The repository signpost fits the maintainer's retirement decision but cannot redirect an old
website URL. Do not report a repository link as an HTTP redirect or promise old URLs still work.

Workshop M0 verifies the published replacement and the repository link. Any future revival of a
redirect endpoint is a new decision, not an unfulfilled acceptance criterion of this one.
