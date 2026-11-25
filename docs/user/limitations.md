# Limitations — `ciphertls-io/core`

This page states what `ciphertls-io/core` **cannot** do, **deliberately does not** do, and
**has not yet built**. Where this page disagrees with a roadmap or mockup, this page wins.

## Current maturity (hard ceiling today)

- **Scaffold only (`M6.0` spike in progress):** No Go library packages under `pkg/` are
  implemented or importable yet. `cmd/ciphertls-core` prints a scaffold stub. Every milestone in
  [docs/PHASES.md](../PHASES.md) remains uncompleted until its acceptance script passes with
  `skeleton: false`.
- **No validated maintenance savings or user demand:** Comparing companion effort against direct
  OpenSSL plus manuals is an explicit `M6.2`/`M7` requirement (`R-18`), not an established fact.

## Design ceilings (by construction, never chased)

1. **No cryptographic re-implementation or OpenSSL fork.** `core` invokes unmodified OpenSSL
   binaries and parses their bounded outputs; it never reimplements X.509 path verification, TLS
   handshakes, or cryptographic primitives.
2. **Unsupported TLS stacks fail closed.** On macOS, `/usr/bin/openssl` is LibreSSL, and `curl`,
   Python, and Node may link different libraries. `core` reports non-OpenSSL or unlisted builds as
   unsupported rather than silently substituting another binary or falling back to latest docs.
3. **An expiry observation (`M6`) is never a trust verdict.** `certificate.expiry` reads `notAfter`
   from a single local PEM file. It does not check trust chains, hostnames, EKUs, revocation
   (CRL/OCSP), or remote servers.
4. **Host inspection is not service-level proof.** Probing a binary on `PATH` does not prove what
   a running daemon, container, or language runtime links unless that specific runtime is probed.
5. **Models never generate shell commands or substitute for checks.** In optional `M7`, natural
   language can only select a reviewed task ID and propose typed slots; `pkg/bridge` validates every
   input and invokes binaries directly without a shell.
6. **Automatic fetch is not automatic adoption.** Refreshing an upstream manual snapshot surfaces
   diffs and triggers fixture re-runs; updating expected outcomes or explanations remains a human
   review step.
7. **No private-key custody or broker yet.** Default output/log redaction starts at artifact
   ingestion (`R-10`), but any OpenSSL Provider / broker custody layer ("use without possession")
   is separate future scope requiring its own design and decision record.
