# Setup review — ciphertls-io/core

Baseline review: 2026-11-11; scope updated 2026-11-12. Standard: method.programs v1.1.
Reference: [k3sm](https://github.com/k3sm-io/k3sm/tree/8429efe09bf880ac847df6a335db9f51022b8efe),
plus its public site observed 2026-11-11. The pinned phase ledger was rechecked 2026-11-12.

Overall verdict: **baseline replication in progress**. The documentation and commit workflow
have been reworked; the first product journey and audience-accessible release are not demonstrated.

| Baseline responsibility | Project artifact | Evidence / state | Remaining gap |
|---|---|---|---|
| Public first-use path | README, docs/user/README.md | Described; scaffold is prominent | Working installation and first task; rendered public path |
| Design and challenged assumptions | docs/DESIGN.md, core DR-008/companion contract | Described; numbered sections and review findings recorded | Exact build/source mapping and M6 spike results |
| Repository boundaries and contracts | Core docs/program.md; design §6 | Described; products depend independently on core | Exercised consumer API and real revision pin |
| Roadmap → phases → gates | ROADMAP.md, docs/PHASES.md, manifest, hack/ci.sh | Commit workflow exercised separately from product proof | M6 acceptance; full ledger/readiness validation |
| Contributor journey and governance | CONTRIBUTING.md, AGENTS.md, existing governance files | Local gate documented | Fresh-clone contribution walkthrough and runnable first issue |
| Release and documentation consistency | docs/user/README.md; roadmap | Unavailable product explicitly labelled | Tagged usable artifact/preview and captured output |

## Review procedure

Run `sh hack/ci.sh` and record the actual result in the PR. It enforces formatting, vet,
Go tests/build, privacy scan, dispatch regression tests and the manifest. It does not make
pending skeleton or manual gates green. Run `python3 hack/acceptance.py --phase M6` for actual
M6 proof. Full YAML parsing and cross-repository readiness are not certified by the dispatcher.

Trace one claim through public instructions, requirements, design, phase, implementation,
acceptance and the release consumers can obtain. Record revision, environment, inputs and
observed output. A populated file map is insufficient.

## Next proof

Core M6.0 must establish exact build/source mapping, output parsing and resource limits.
Then prove applicable/offline references and explicit read-only expiry without an NL runtime.
M7 is optional. Demand, maintenance savings and product accuracy are still unvalidated.
Preserve existing IDs and add evidence when the behavior is observed. This review does not move
any product deliverable to done.

## Local validation recorded in this rework

Verified 2026-11-11 on the working tree: `sh hack/ci.sh` exited 0. Its 11 dispatch regression
cases passed; Go vet/test/build and the tracked-file privacy scan passed. Go product packages
still report no test files. Every product milestone remains a pending skeleton. The result
certifies the commit workflow only; no product acceptance or baseline demonstration is claimed.

## Companion setup validation — 2026-11-12

On the local working tree, `sh hack/ci.sh` exited 0: formatting, Go vet/test/build, tracked-file
privacy scan, 11 dispatch regression cases and manifest dispatch passed. Go product packages
still report no test files. The manifest reports product gates as pending, not completed.

An additional setup check parsed all three YAML ledgers, checked retained phase/deliverable IDs
against HEAD, matched phase IDs to manifests, checked declared cross-repository dependencies for
existence/cycles, resolved local Markdown links and scanned tracked plus untracked project files
for private terms. These checks passed; they do not establish remote dependency readiness.

Explicit `python3 hack/acceptance.py --phase M6` and `--phase M7` each exited 1 because their
always-red skeletons remain. No reference pack, bridge, model, consumer install or preview was
demonstrated. Baseline replication remains in progress. Changes are uncommitted and unpublished.

## Parity pass and M6.0 spike — 2026-11-24

- Renamed and moved to `cipherTLS-io/core` per [DR-009](decisions/DR-009-ciphertls-org-and-rename.md).
- Unified mission across README/PRD/companion design; added [limitations](user/limitations.md),
  one-way-doors table, named Go acceptance test targets (`TestM6_*`/`TestM7_*`), and phase reading
  order callouts.
- Upgraded `hack/ci.sh` to match `k3sm` / `method.programs` v1.1 local-gate discipline (`ci_done=1`
  fail-closed trap, fail-closed `go list`, `go test -race`, `go mod tidy` no-diff, local DCO
  sign-off verification) and removed `.github/workflows/`.
- Completed `M6.0` spike (`python3 -B hack/spike/m6.py`, `V1`–`V6` PASS) in
  [docs/findings-m6.md](findings-m6.md). `M6.1`–`M6.4` remain `todo`; verdict remains **baseline
  assembled (`M6.0` validated), not yet baseline demonstrated**.
