# ciphertls-io/core

**Availability: scaffold + M6.0 spike; no working product release is claimed.** The examples below
illustrate planned behavior. Start with the [current quickstart](docs/user/README.md),
[limitations](docs/user/limitations.md), [setup gaps](docs/setup-review.md), and
[design](docs/DESIGN.md).

**CipherTLS** makes real OpenSSL verifiable, version-accurate, and safe for both humans and AI
agents — without hiding the binary, hallucinating flags, or handing secrets to a model.

This repository (`ciphertls-io/core`, binary `ciphertls-core`) is the **ground-truth engine**
under [aitls](https://github.com/ciphertls-io/aitls) and
[openssl-workshop](https://github.com/ciphertls-io/openssl-workshop). It has no user interface of
its own. Deterministic grounding (`M6`) comes first because it is the guardrail that makes AI safe
for cryptography: identify the explicitly selected OpenSSL build, bind an immutable upstream manual
snapshot, and run reviewed read-only tasks through a typed, shell-free bridge. Optional
natural-language task selection (`M7`) is evaluated against that deterministic baseline; container
checks (`M0`+) and workshop lessons remain later slices.

Selected direction: [program map](docs/program.md), [companion design](docs/design/companion.md),
[DR-008](docs/decisions/DR-008-companion-first.md), and
[DR-009](docs/decisions/DR-009-ciphertls-org-and-rename.md). **M6** is the first delivery target.

Maintainer: `veronaimars`. License: Apache-2.0.

## Why not just `openssl` + `man` or a raw LLM in a shell?

- **Adding another crypto tool adds another TLS stack.** On macOS, `/usr/bin/openssl` (LibreSSL),
  Homebrew OpenSSL, Python, Node, and `curl` already disagree (see [DESIGN.md §2](docs/DESIGN.md#2-the-core-constraint)).
- **Manuals and runbooks drift silently.** Downstream guides and AI prompts routinely cite EOL
  OpenSSL 1.1.1 flags or removed 3.x options against OpenSSL 4.x binaries.
- **Raw LLMs in a shell have no guardrails.** They guess removed flags, build shell strings from
  untrusted paths, confuse exit-code-0 inspection with cryptographic trust, and read private keys
  into transcripts.

`core` solves the shared half once: exact binary identity, immutable upstream doc snapshots,
selective drift detection, and a typed `companion-result/v1` contract that separates execution
errors, observations, and cryptographic assertions.

## A look (`v0.1` M6 target — illustrative, not yet captured from a release)

```text
$ ciphertls-core doc --binary /opt/homebrew/bin/openssl x509 -enddate
binary:   /opt/homebrew/bin/openssl  (OpenSSL 4.1.0, 23 Oct 2026)
source:   openssl/openssl@openssl-4.1.0  doc/man1/openssl-x509.pod.in (sha256: <pinned>)
section:  OPTIONS / -enddate   last_checked: 2026-11-24T00:00:00Z (offline-ok)
  Prints out the expiry date of the certificate, that is the notAfter date.

$ ciphertls-core task run certificate.expiry --binary /opt/homebrew/bin/openssl --cert leaf.pem
plan:     /opt/homebrew/bin/openssl x509 -in leaf.pem -noout -enddate
status:   completed (exit 0)
observed: notAfter=Nov 24 21:00:00 2027 GMT  (2027-11-24T21:00:00Z)
verdict:  none (expiry observation only; not trust, hostname, or revocation validation)
```

*Illustrative output only, not a captured release run. Published examples must come from real
acceptance runs once M6 ships.*

## Later roadmap (`M0`+ shared engine)

After `M6`/`M7`, `core` adds pinned release containers, full-corpus option diffs across releases,
and shared verification rules for both `aitls` and `openssl-workshop`. See [ROADMAP.md](ROADMAP.md)
and [docs/PHASES.md](docs/PHASES.md).

## Status & Layout

Scaffold + `M6.0` spike. Delivery state lives in [docs/PHASES.md](docs/PHASES.md), proven by
[hack/acceptance/phases.json](hack/acceptance/phases.json). Program PRD: [docs/PRD.md](docs/PRD.md).

| Path | What |
|---|---|
| `cmd/ciphertls-core/` | CLI entry point (scaffold stub until M6.1) |
| `pkg/` | Package boundaries: `pkg/reference`, `pkg/bridge`, optional `pkg/nl` (M6/M7); later `corpus`, `check`, `sandbox`, `host` |
| `packs/` | Reference, workflow, and optional NL pack boundaries |
| `corpus/` | OpenSSL manuals copied by release tag (M0+); see `THIRD_PARTY.md` |
| `rules/` | Shared correctness and policy rules (`rules/correctness/` first) |
| `docs/` | `PRD.md`, `program.md`, `DESIGN.md`, `design/companion.md`, `PHASES.md`, `decisions/`, `user/` |
| `hack/` | `ci.sh` (local commit gate), `check.sh` (privacy scrub), `acceptance.py`, `spike/` |

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md): run `sh hack/ci.sh` locally, sign off every commit
(`git commit -s`), follow Conventional Commits, and read [GOVERNANCE.md](GOVERNANCE.md),
[MAINTAINERS.md](MAINTAINERS.md), and [SECURITY.md](SECURITY.md).

## Licence

Apache-2.0 ([LICENSE](LICENSE)). OpenSSL documentation under `corpus/` keeps its own Apache-2.0
notices; see [THIRD_PARTY.md](THIRD_PARTY.md) and [NOTICE](NOTICE).
