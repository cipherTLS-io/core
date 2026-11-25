---
repo: core
schema: phases/v1
current_phase: M6
updated: 2026-11-24
updated_by: veronaimars
phases:
  - id: M6
    title: "Documentation companion and read-only expiry bridge (design \u00a75.6; R12\u2013R18)"
    status: todo
    depends_on: []
    subphases:
      - id: M6.0
        title: "Spike \u2014 exact build, applicable source, parser and limits"
        status: done
        completed: 2026-11-24
        deliverables:
          - id: M6.0-d1
            done: true
            proof: "python3 -B hack/spike/m6.py (V1-V6 PASS on macOS arm64, OpenSSL 4.1.0 + LibreSSL 3.3.6 + openssl-4.1.0 tag commit 2f4e3a5f); recorded in docs/findings-m6.md"
            desc: "docs/findings-m6.md records the tested executable/build, upstream source mapping, certificate/output parsing, config/provider environment and concrete resource limits; unknown mappings remain unsupported"
      - id: M6.1
        title: "Reference pack and refresh"
        status: todo
        deliverables:
          - id: M6.1-d1
            done: false
            desc: "Implement pkg/reference and one immutable reference pack with source commit/hash/section, applicability, last successful refresh and affected-workflow IDs"
        acceptance:
          - id: M6.1-a1
            met: false
            check: "Offline lookup retains the selected snapshot; failed refresh, wrong implementation/release, moved source and changed/removed/unparsable sections are visible; latest docs never silently replace execution context"
            method: e2e
      - id: M6.2
        title: "Typed read-only bridge"
        status: todo
        deliverables:
          - id: M6.2-d1
            done: false
            desc: "Implement certificate.expiry through pkg/bridge using validated argv and explicit execution; record companion-result/v1 with expiry observation and provenance"
        acceptance:
          - id: M6.2-a1
            met: false
            check: "Good and expired certificates return expiry observations, never trust verdicts; malformed, missing, oversized and unsupported inputs are execution errors; paths with shell metacharacters cannot execute shell code; changed input or executable invalidates the plan"
            method: e2e
      - id: M6.3
        title: "Consumer contract without a model"
        status: todo
        deliverables:
          - id: M6.3-d1
            done: false
            desc: "Expose and document the provisional reference/bridge interfaces and pack versions for terminal M6; reference retrieval and expiry require no NL runtime or external source service"
        acceptance:
          - id: M6.3-a1
            met: false
            check: "A consumer using a recorded core revision accepts the result schema and rejects unsupported versions; no private artifact bytes appear in reference requests or default output/logs"
            method: e2e
      - id: M6.4
        title: "gate"
        status: todo
        deliverables:
          - id: M6.4-d1
            done: false
            desc: "Replace hack/acceptance/m6.sh with real proof and flip skeleton to false in the same change; record exact supported builds and clean-checkout evidence"
  - id: M7
    title: "Optional NL task selection experiment (design \u00a75.6; R15, R18)"
    status: todo
    depends_on: ["M6"]
    subphases:
      - id: M7.0
        title: "Spike \u2014 baseline and held-out evaluation"
        status: todo
        deliverables:
          - id: M7.0-d1
            done: false
            desc: "Record deterministic task-selection baseline, held-out paraphrase/negation/ambiguity/out-of-scope cases and a candidate router; no model is preselected"
      - id: M7.1
        title: "Optional router boundary"
        status: todo
        deliverables:
          - id: M7.1-d1
            done: false
            desc: "Implement pkg/nl as a separately enabled adapter returning matched task ID and typed slots, clarify, or unsupported; retain the ordinary task path"
        acceptance:
          - id: M7.1-a1
            met: false
            check: "NL cannot supply argv, change sources or execute; ambiguous, negated and out-of-scope requests do not run the task; held-out results compare correct routing, abstention, latency and maintenance against the baseline"
            method: e2e
      - id: M7.2
        title: "gate"
        status: todo
        deliverables:
          - id: M7.2-d1
            done: false
            desc: "Replace hack/acceptance/m7.sh with actual comparison evidence; document keep, revise or drop decision before treating NL as a product feature"
  - id: M0
    title: "Walking skeleton — one man page by tag, indexed and diffed; one check in a pinned container; the host probed (design §7)"
    status: todo
    depends_on: [M6]
    subphases:
      - id: M0.0
        title: "Spike — copy one man page by tag, index it, run one check in a container (design §4)"
        status: todo
        deliverables:
          - id: M0.0-d1
            done: false
            desc: "docs/findings-m0.md records the POD-to-index shape, container build time, and the check output contract"
      - id: M0.1
        title: "Release images (design §4 Images)"
        status: todo
        deliverables:
          - id: M0.1-d1
            done: false
            desc: "images/openssl-3.5 and images/openssl-4.1 build from the tagged OpenSSL source; `openssl version` inside each prints the pinned release"
      - id: M0.2
        title: "Corpus and diff for one command (design §5.1, §5.2)"
        status: todo
        deliverables:
          - id: M0.2-d1
            done: false
            desc: "`ciphertls-core corpus build` copies doc/ at each tag into corpus/<version>/ with SOURCE (tag, commit) and indexes man1/openssl-verify"
          - id: M0.2-d2
            done: false
            desc: "`ciphertls-core corpus diff 3.5 4.1 openssl-verify` lists added and removed options"
      - id: M0.3
        title: "One check in a container (design §5.3, §5.4)"
        status: todo
        deliverables:
          - id: M0.3-d1
            done: false
            desc: "Run the chain rule with named artifact bindings in exact pinned images; emit separate execution, observation and assertion states with the provenance and citation contract of design §5.4"
          - id: M0.3-d2
            done: false
            desc: "Expose the narrow provisional Go API to both consumers; reject unsupported result schema versions and document the reviewed core revision used by each"
        acceptance:
          - id: M0.3-a1
            met: false
            check: "Fresh clone and exact release images: complete chain verifies; omitted intermediate produces the intended invalid-chain reason; missing file and sandbox errors never satisfy the negative assertion; result records exact provenance and citation"
            method: e2e
          - id: M0.3-a2
            met: false
            check: "Reference consumer fixtures accept the versioned result and reject unsupported schemas; mismatched host release does not alter its citation; default logs/results contain no sensitive fixture bytes"
            method: integration
      - id: M0.4
        title: "The host probe (design §5.0)"
        status: todo
        deliverables:
          - id: M0.4-d1
            done: false
            desc: "`ciphertls-core host probe` prints the stack identity JSON of design §5.0 on this Mac (LibreSSL at /usr/bin, OpenSSL from Homebrew, Python, Node, curl each read the way they report it) and on a Linux CI runner; mismatches are listed as facts"
      - id: M0.5
        title: "gate"
        status: todo
        deliverables:
          - id: M0.5-d1
            done: false
            desc: "hack/acceptance/m0.sh replaces the skeleton; phases.json flips skeleton to false in the same change"
  - id: M1
    title: "The whole corpus, rebuilt nightly"
    status: todo
    depends_on: [M0]
    subphases:
      - id: M1.1
        title: "man1, man5, man7 for every supported release; -help captured (design §5.1)"
        status: todo
        deliverables:
          - id: M1.1-d1
            done: false
            desc: "every release selected by the explicit support matrix is copied at an immutable source commit and indexed; `ciphertls-core help capture` records each subcommand's -help from the pinned image beside the index"
          - id: M1.1-d2
            done: false
            desc: "a nightly workflow discovers new release snapshots, preserves prior source identities, flags moved tags and emits changed/removed/unparsable section records for consumer revalidation"
      - id: M1.2
        title: "gate"
        status: todo
        deliverables:
          - id: M1.2-d1
            done: false
            desc: "hack/acceptance/m1.sh replaces the skeleton"
  - id: M2
    title: "Checks as files and a stable API"
    status: todo
    depends_on: [M1]
    subphases:
      - id: M2.1
        title: "pkg/corpus, pkg/check, pkg/sandbox (design §4 Components)"
        status: todo
        deliverables:
          - id: M2.1-d1
            done: false
            desc: "stabilize the provisional Go interface consumed since M0; document compatibility, check schema and correctness rules; the products retain real revision/release pins"
      - id: M2.2
        title: "gate"
        status: todo
        deliverables:
          - id: M2.2-d1
            done: false
            desc: "hack/acceptance/m2.sh replaces the skeleton"
---

# Phases

The YAML frontmatter is authoritative; the prose after it is a mirror for reading. One command per
phase proves it: `hack/acceptance/phases.json` maps each gate to the script whose exit 0 means
met. A gate that does not exist yet ships as an always-red skeleton so nothing is green by
accident.

Phases are `M<N>`, sub-phases `M<N>.<k>`; `.0` is a spike whose findings are written back as
`docs/findings-m<N>.md`, the middle numbers are deliverables, the last is the gate that swaps the
skeleton for the real acceptance script. Deliverable ids are `M<N>.<k>-d<j>`, acceptance ids
`M<N>.<k>-a<j>`. Status: ✅ done, 🟡 in progress, ⛔ blocked, ⬜ todo. Completion dates and proof are added only when the behavior is demonstrated.


## Reading order (why M6 comes before M0)

> Phase IDs are permanent identifiers (never renumbered once cited in issues and decision records).
> Under [DR-008](decisions/DR-008-companion-first.md), delivery order is **`M6` (`v0.1` companion
> foundation) → optional `M7` (NL router eval) → `M0`–`M2` (later container engine & corpus)**.
> `M6.0` is ✅ done (`docs/findings-m6.md`, 2026-11-24); `M6.1`–`M6.4` remain ⬜ todo.

## Mirror

Selected delivery order (2026-11-12): **M6 first**, then evaluate optional **M7**.
M0 and its successors retain their IDs for later chain/learning/workbench work.
Phase numbers are stable identifiers, not execution order; follow dependencies.

- **M6** proves the documentation companion and one read-only expiry task without a model.
- **M7** evaluates optional NL selection against the ordinary task path; it is not required for M6.

- **M0** proves the whole loop once: pinned OpenSSL in a container, a manual copied by tag and
  indexed, a diff between two releases, one check that reads real artifacts, and the host probed.
- **M1** widens the copy to every supported release and every page, and makes change visible every
  night.
- **M2** stabilizes the provisional M0 consumer interface and its compatibility contract.
