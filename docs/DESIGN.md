# ciphertls-io/core — design

**Direction update, 2026-11-12:** [DR-008](decisions/DR-008-companion-first.md) selects the documentation companion
and one read-only expiry task first (core/terminal M6); NL is an optional M7 experiment.
The earlier chain/learning/workbench designs below remain later scope. Contracts and gates are
acceptance targets, not implemented behavior; historical measurements retain their original dates.


> **`ciphertls-io/core`**: the engine under the OpenSSL Workshop and AITLS. It holds
> OpenSSL's own manuals copied at each release tag and indexed, the option diff between releases, one container
> image per release built from the tagged source, checks that run the real `openssl` against real artifacts, and
> a probe that says which TLS stack a host actually has. One Go module, one binary `ciphertls-core`, no user interface.
>
> **Status: v1.1 (2026-11-11), pre-spike; contract revision proposed.** Every ✅ below names the command that verified it; ⚠️ is verified but
> constrained; ⬜ is a design claim the M0 spike must confirm. The M0 findings (`docs/findings-m0.md`) revise this
> file. Phases in `docs/PHASES.md` cite sections here by number. Historical measurements below are dated
> observations carried from v1; they are not re-run evidence for this revision.

## 1. Context

OpenSSL keeps its documentation current and changes it often. Verified 2026-11-07 against
<https://github.com/openssl/openssl>: observed branches `openssl-3.0` through `openssl-3.6`, `openssl-4.0` and
`master` (branch presence does not establish support); latest releases 4.1.0 (2026-10-23) and 4.0.3, 3.6.5, 3.5.9, 3.4.8 (2026-09-29); 145 commits touching
`doc/` in the three months to 2026-11-07; `doc/` holds 63 man1 pages, 685 man3, 3 man5, 169 man7. One page as an
example: `doc/man1/openssl-req.pod.in` grew from 788 lines at 3.0 to 880 at master, gaining `-cipher`,
`-not_before`, `-not_after`, `-quiet` and `-x509v1` and losing `-keygen_engine` with the ENGINE API.
<https://docs.openssl.org> is generated from the same files by <https://github.com/openssl/openssl-docs>, so a
section here comes from the same source family. For support status use the
[official downloads/lifecycle table](https://openssl-library.org/source/), checked 2026-11-11;
do not infer it from the dated branch inventory above.

Material built on top of these manuals goes stale silently. The maintainer's own 2025 teaching module is the
example: it taught OpenSSL 1.1.1 (end of life 2023-09-11), checked a chain with `verify -CAfile full_chain.pem`
(which passes on 3.6.4 even when the intermediate is missing; verified by running it), and matched keys by RSA
modulus (`req -noout -modulus` on an EC key prints `Wrong Algorithm type`; verified). Nobody noticed for a year.

Two products need the same answers from these sources: lessons that grade a learner's artifact, and a
workbench that reports findings on an operator's artifact. Neither should carry its own copy of the manuals or
its own idea of what `openssl verify` does. That shared layer is this repository.

## 2. The core constraint

**The manual is per release, and the `openssl` in front of a person is one of several on the same machine.**
A tutor that quotes the wrong release, or a check that runs against a different binary than the one a program
links, is wrong in a way the reader cannot see. Measured on the maintainer's Mac on 2026-11-07:

| What | Command | Result |
|---|---|---|
| The `openssl` on `PATH` | `which -a openssl` | `/opt/homebrew/bin/openssl`, then `/usr/bin/openssl` |
| Apple's | `/usr/bin/openssl version` | ✅ **LibreSSL 3.3.6**, not OpenSSL |
| Homebrew's | `openssl version -a` | ✅ OpenSSL 3.6.4, `OPENSSLDIR: /opt/homebrew/etc/openssl@3`, its own `MODULESDIR` |
| What Python links | `python3 -c 'import ssl; print(ssl.OPENSSL_VERSION)'` | ✅ OpenSSL 3.6.4 |
| What Node bundles | `node -p process.versions.openssl` | ✅ 3.5.4 |
| What curl uses | `curl --version` | ✅ SecureTransport + LibreSSL 3.3.6 |
| Active providers | `openssl list -providers` | ✅ default only; no FIPS provider loaded |
| Config override | `env \| grep OPENSSL` | ✅ none set; `OPENSSL_CONF` and `OPENSSL_MODULES` would change the answers above |

Five TLS stacks on one machine, three of them not the one `openssl` on `PATH` is. This is the "the OS is doing
one thing and a system calls for another" problem, and it is the gap between what is taught and what operators
meet. The design treats it as a first-class object, not a footnote (§5.0).

Consequences the rest of the design bends to:

- **Grading never depends on the host.** Every later chain/lesson check runs the real `openssl` inside a container image built from
  the release tag the lesson or rule names (§5.3). The host's stack is probed and reported, never used to grade.
- **Explanation always names the release.** An answer is "in 4.1.0, `openssl-verify(1)` OPTIONS says …", never
  "OpenSSL says …". Where the host is not OpenSSL (LibreSSL, BoringSSL), the engine says so and stops; it has no
  manual for those stacks (unassigned: a LibreSSL corpus).
- **The corpus is a copy, never a paraphrase.** `corpus/<version>/doc/` is the upstream tree at the tag, byte for
  byte, under its own Apache-2.0 notices (`THIRD_PARTY.md`). Everything the engine says cites into it.

## 3. Review findings and revisions

2026-11-12 scope review: the maintainer selected a smaller first task to test maintenance value.
A documentation companion must prove applicable sources and accurate output before adding NL.
DR-008 changes delivery order, preserves historical phase IDs, and removes the workshop from
the first companion dependency path. No runtime result or market validation is claimed.

Design review, 2026-11-11: the prior outlines did not consistently distinguish execution errors,
cryptographic outcomes and learner assessment; product dependencies and explanation context also
needed reconciliation. The first shared journey now references the core evidence contract and
DR-007 (proposed; maintainer review pending). No product acceptance or external review is claimed.

Section migration: old §3 architecture → §4; old §4 platform model → §5.0; §5.1 onward and
§6–§9 retain their IDs. §10 now includes prerequisites. References in this repository were migrated
with the change; old revisions retain the original map.

## 4. Architecture

The first companion flow is `selected executable + reference pack → typed task → reviewed plan →
explicit execution → expiry observation + cited source`. See §5.6 for ownership and boundaries.
The following diagram is the later container/check architecture.

```text
openssl/openssl tags ──sparse copy──▶ corpus/<ver>/doc/ ──POD parse──▶ corpus/<ver>/index.json ──▶ pkg/diff
                     └──build from source──▶ image openssl:<ver> ──┐
rules/<set>/<check>.yaml + artifacts ───────────────────────────────┴──▶ pkg/sandbox ──▶ pkg/check ──▶ findings (JSON)
host (PATH, libraries, config, providers) ──▶ pkg/host probe ──▶ stack identity (JSON)

workshop ──imports──▶ pkg/{corpus,diff,check,sandbox,host}  ◀──imports── terminal
```

Data flows one way: from OpenSSL's tags into the corpus and images, from rules and artifacts through the sandbox
into findings. The products import the packages and pin a release of this module; this module imports neither.

### Components

| Component | Responsibility | Path |
|---|---|---|
| CLI | `corpus build\|diff`, `images build`, `check run`, `host probe`; the same calls the packages expose | `cmd/ciphertls-core/` |
| Corpus | sparse copy of `doc/` at a tag; `SOURCE` record; POD parser; per-release `index.json` | `pkg/corpus/`, `corpus/` |
| Diff | options added, removed, reworded between two releases, per page | `pkg/diff/` |
| Images | one `Containerfile` per release, built from the tagged source, `openssl version` asserted | `pkg/images/`, `images/` |
| Sandbox | run one command in one image with a mounted work directory; capture exit, stdout, stderr | `pkg/sandbox/` |
| Check | schema for `rules/*.yaml`; run a check's commands in the sandbox; produce findings | `pkg/check/`, `rules/` |
| Host | probe which binaries, libraries, config and providers a machine has; report mismatches | `pkg/host/` |
| Finding | the one output contract both products consume (§5.4) | `pkg/finding/` |

## 5. Subsystem designs

### 5.0. The host platform model

A **stack identity** is what `ciphertls-core host probe` reports. It is the object the terminal shows first and the
workshop's "the stack you actually have" lessons teach from.

```json
{
  "probed_at": "2026-11-07T20:10:00Z",
  "os": {"name": "darwin", "version": "25.6.0", "arch": "arm64"},
  "binaries": [
    {"path": "/opt/homebrew/bin/openssl", "impl": "openssl", "version": "3.6.4", "on_path": 1,
     "openssldir": "/opt/homebrew/etc/openssl@3", "modulesdir": "/opt/homebrew/Cellar/openssl@3/3.6.4/lib/ossl-modules",
     "providers": ["default"]},
    {"path": "/usr/bin/openssl", "impl": "libressl", "version": "3.3.6", "on_path": 2}
  ],
  "consumers": [
    {"name": "python3", "impl": "openssl", "version": "3.6.4", "how": "ssl.OPENSSL_VERSION"},
    {"name": "node",    "impl": "openssl", "version": "3.5.4", "how": "process.versions.openssl"},
    {"name": "curl",    "impl": "securetransport+libressl", "version": "3.3.6", "how": "curl --version"}
  ],
  "env": {"OPENSSL_CONF": null, "OPENSSL_MODULES": null},
  "mismatches": [
    {"kind": "path-vs-consumer", "detail": "node bundles 3.5.4; openssl on PATH is 3.6.4"},
    {"kind": "impl", "detail": "/usr/bin/openssl is LibreSSL; the manual corpus covers OpenSSL only"}
  ]
}
```

Rules:

- `impl` is read from `version` output, never guessed from the path. LibreSSL and BoringSSL are reported, not
  explained; the engine has no corpus for them.
- A consumer's library version is read the way that consumer reports it (the `how` field), so the probe is a
  record of commands anyone can rerun.
- A mismatch is a fact, not a verdict. The terminal turns it into a finding with a rule set; a lesson turns it
  into a question. The core only reports.
- ⬜ Linux probing (distro packages, `ldd` on a consumer, `OPENSSL_CONF` in a service unit) is designed the same
  way and confirmed in the M0 spike on a Linux runner.


Written out so a reader, human or model, cites them instead of inferring them.

### 5.1 Corpus record

`corpus/<version>/SOURCE`:

```text
repo    https://github.com/openssl/openssl
tag     openssl-4.1.0
commit  <40-hex>
copied  2026-11-07T19:02:11Z
paths   doc/man1 doc/man3 doc/man5 doc/man7
```

`corpus/<version>/index.json` (one entry per page; options are entries too):

```json
{"version": "4.1.0", "pages": [
  {"page": "openssl-verify", "section_no": 1, "file": "doc/man1/openssl-verify.pod",
   "url": "https://docs.openssl.org/4.1/man1/openssl-verify/",
   "sections": [
     {"name": "OPTIONS", "lines": [38, 212], "text_sha256": "…",
      "options": [
        {"name": "-untrusted", "arg": "filename", "lines": [96, 101],
         "text": "A file of untrusted certificates. The file should contain one or more certificates in PEM format."}
      ]}
   ]}
]}
```

⬜ The `url` pattern follows the openssl-docs site layout and is confirmed in M0.0. `lines` are line numbers in
the copied file so a citation can point at bytes, not prose.

### 5.2 Diff

`ciphertls-core corpus diff 3.5 4.1 openssl-req` prints, and `pkg/diff` returns:

```json
{"page": "openssl-req", "from": "3.5.9", "to": "4.1.0",
 "added":   [{"name": "-not_before", "section": "OPTIONS"}],
 "removed": [{"name": "-keygen_engine", "section": "OPTIONS"}],
 "changed": [{"name": "-newkey", "section": "OPTIONS", "lines_changed": 12}]}
```

### 5.3 Check

Reusable verification commands live in `rules/`; products bind named inputs and consume results.
The first draft rule uses an argument vector and declared input placeholders, not a shell command
assembled from a user path. Missing inputs are execution errors. A negative check requires an
explicit expected cryptographic outcome and reason, not just any nonzero exit.

See [the rule draft](../rules/correctness/chain.yaml) and [rule authoring](../rules/README.md).
The runner and rule parser remain unimplemented; DR-007 proposes this correction to the earlier
`expect_fail`-only schema. Reviewed fixture setup is separate from verification and assessment.

### 5.4 Finding

The [shared evidence contract](design/evidence-contract.md) owns the result fields and provenance.
Execution, observation and assertion are separate: a completed verification can observe an
invalid chain while meeting a negative assertion. Input errors and sandbox errors cannot pass it.
Each product adds its own interpretation without changing the underlying observation.

The earlier single `status: fail` example was ambiguous and is superseded by this proposed M0
contract. No result encoder or consumer API is implemented yet. Core M0 exposes a narrow provisional
Go API, used by both consumers at a recorded revision; M2 stabilizes it.

### 5.5 Sandbox, provenance and drift

The [shared contract](design/evidence-contract.md) specifies input/trust/time identities, immutable
source snapshots, pinned image digests, release-bound explanation, default artifact handling and
separate documentation-change review. Exact runtime limits must be established in the M0 spike.
The first drift feed compares released snapshots; development-branch monitoring is later scope.

### 5.6 Companion, reference packs and optional NL

[Companion design](design/companion.md) owns the M6/M7 contracts, R12–R18, source freshness
channels, typed plans, `companion-result/v1`, applicability failures, update review and the baseline
comparison. [Package boundaries](../packs/README.md) distinguish source references, reviewed
workflows and optional NL routing. These boundaries do not claim importable implementations.

An external source service can later replace fetching/caching behind the same adapter. It is
not required now. The model, if adopted, chooses a task ID and typed inputs; commands and
changing documentation remain in reviewed data and deterministic code.

## 6. Repositories

Three execution repositories, one program (DR-003): `ciphertls-io/core` (this one), `openssl-workshop` (lessons,
generator, site, drift re-run), `aitls` (parsers, trust graph, findings, panes). The products
import this module and pin a release; it never imports them. The program PRD is `docs/PRD.md` here.

## 7. Build roadmap as slices

Selected first: **M6** companion without a model; optional **M7** NL comparison.
The numbered IDs below retain their meaning and follow later; dependencies define order.

One thin end-to-end slice per phase; the gate in `hack/acceptance/` proves it.

- **M0** one page by tag, indexed and diffed (§5.1, §5.2); two exact release images (§4 Images); one check
  producing the shared result and citation (§5.3–§5.5); the host probe (§5.0); the narrow provisional
  consumer API. Prove valid, intended-invalid and input/runtime-error cases. Findings revise this design.
- **M1** every supported release, every page, `-help` captured, nightly rebuild with the changed-section list.
- **M2** `pkg/` stable; checks with parsed-field assertions; the correctness set documented.
- **M3** named rule sets (FIPS 140-3, NIST SP 800-52, CA/Browser Forum baseline, Mozilla); each finding cites the
  rule and the manual section.
- **M4** man3 indexed.

## 8. Risks and mitigations

| Risk | Verified how | Mitigation |
|---|---|---|
| POD is irregular across 900 pages; a parser that handles man1 breaks on man3 | ⬜ M0.0 parses one page; M1 runs the parser over all and counts failures | index only what parses; list unparsed pages in the build output; never silently drop a section |
| Building OpenSSL from source per release is slow in CI | ⬜ M0.1 records the time | cache layers and record image digests; detect moved tags without replacing recorded source snapshots |
| The host probe reports a stack the engine cannot explain (LibreSSL on every Mac) | ✅ `/usr/bin/openssl version` → LibreSSL 3.3.6 | report `impl`, say the corpus covers OpenSSL only, point at the on-PATH OpenSSL if there is one |
| A check author writes an expectation that is true on one release and false on another | ✅ the `verify -CAfile full_chain.pem` case: passes on 3.6.4, taught as a failure | every check lists its versions and CI runs each; a check is not merged unless it passes on all it lists |
| The copied manuals carry licence obligations | ✅ Apache-2.0 per-file notices in `doc/` | copy unmodified, keep notices, `THIRD_PARTY.md`, never edit |

## 9. Verification

Current companion proof: `python3 hack/acceptance.py --phase M6`; optional router proof:
`python3 hack/acceptance.py --phase M7`. Both deliberately fail as skeletons until implemented.
The commands below are planned later chain checks, not available product commands.

```sh
ciphertls-core corpus build --version 3.5 --version 4.1 --page openssl-verify   # §5.1: SOURCE + index.json exist
ciphertls-core corpus diff 3.5 4.1 openssl-req                                   # §5.2: added/removed/changed
ciphertls-core images build 3.5 4.1 && docker run --rm openssl:4.1.0 openssl version
ciphertls-core check run rules/correctness/chain.yaml --version 4.1 testdata/chain   # §5.4: one pass, one expected fail
ciphertls-core host probe                                                        # §5.0: stack identity JSON
hack/acceptance/m0.sh                                                        # exit 0 = M0 met
```

## 10. Prerequisites and decisions

DR-008 selects companion-first delivery. Exact executable/source mapping, bounded bridge behavior
and actual consumer evidence remain prerequisites for core/terminal M6. No model or external
source service is required. The following prerequisites describe the later chain journey.

Implementation prerequisites: an exercised core interface, exact release/image/source identities,
and evidence for the first shared journey. These are pending. DR-007 in core proposes the
reconciled contract; design text is a pre-implementation plan, not a runtime claim.


- DR-001 independent, clean-room, open source; sole maintainer.
- DR-002 one Go module and binary; checks as files; real OpenSSL in pinned containers; the copy is never edited.
- DR-003 three repositories, one program; the PRD lives here.
