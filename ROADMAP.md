# Roadmap

Grouped by minor version. What a version contains is the promise; when it ships is not. Delivery
state lives in `docs/PHASES.md` and is proven by `hack/acceptance/phases.json`; this file never
claims more than the ledger does. Entries without ledger rows are uncommitted direction,
not ready work. All versions below are planned; none is a release claim.

One Go module, with reference and deterministic bridge packages first. M6 uses an explicitly
selected existing OpenSSL executable for one read-only task. Container checks remain later scope.
NL is an optional adapter; no model is selected or bundled. See [DR-008](docs/decisions/DR-008-companion-first.md).
Phase IDs are stable identifiers, not delivery order. Earlier version groupings below are replanned.

## Reading order (why M6 comes before M0)

> Phase IDs are permanent identifiers (never renumbered once cited in issues and decision records).
> Under [DR-008](docs/decisions/DR-008-companion-first.md), delivery order is **`M6` (v0.1 companion
> foundation) → optional `M7` (NL router eval) → `M0`–`M2` (later container engine & corpus)**.

## v0.1 target — companion foundation

- M6 applicable reference pack, offline lookup, explicit read-only `certificate.expiry`, versioned
  result, affected-workflow change records and a provisional consumer API without an NL runtime
- Optional M7 experiment: task ID and typed slots from natural language, measured against a
  deterministic baseline; proceed only if accuracy and upkeep justify the extra dependency
- Exact supported builds, source mapping and resource limits are findings required by M6.0

## Later — the three answers for one chain command

- M0 walking skeleton: two release images, the corpus and diff for one command, one check run in a
  container, the host probed, all from a fresh clone
- M1 the whole corpus: man1, man5 and man7 for every supported release, each binary's `-help`
  captured, the index rebuilt nightly and published as a release asset
- M2 checks as files and a stable API: `pkg/corpus`, `pkg/check`, `pkg/sandbox` the products
  import; the correctness rule set

## Later — rule sets and the library manual

- M3 named rule sets beyond correctness: FIPS 140-3, NIST SP 800-52, CA/Browser Forum baseline,
  Mozilla; each finding cites the rule and the manual section
- M4 man3 indexed (the library manual), for the terminal's explain on library-level artifacts

## Not yet assigned

- CHANGES.md indexing per release, mapped to the options it touched
- a link map onto docs.openssl.org, which is built from the same files
- OpenSSL compiled to WebAssembly as a sandbox the browser products can use
