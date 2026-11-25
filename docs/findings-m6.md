# M6.0 spike — host binary identity, upstream source pinning, and bounded expiry bridge ✅ VALIDATED (2026-11-24)

**Goal:** prove the `ciphertls-io/core` M6 premises before writing `pkg/reference` or `pkg/bridge`:
exact host binary identification (OpenSSL vs. LibreSSL), immutable upstream manual pinning,
bounded `certificate.expiry` execution and UTC parsing, error/key-leak rejection, shell-injection
immunity, and selective drift scoping.

**Result: all six spike gates (`V1`–`V6`) passed** on macOS (arm64) against Homebrew **OpenSSL
4.1.0**, Apple **LibreSSL 3.3.6**, and upstream `openssl/openssl` tag `openssl-4.1.0`. Reproducible
runner: **`python3 -B hack/spike/m6.py`**.

## Validation ladder

| # | Gate | Result | Evidence (run 2026-11-24) |
|---|---|---|---|
| V1 | Host binary probe distinguishes OpenSSL vs. LibreSSL | ✅ | `/opt/homebrew/bin/openssl` → `OpenSSL 4.1.0 23 Oct 2026` (`supported: true`, `default` provider 4.1.0 active); `/usr/bin/openssl` → `LibreSSL 3.3.6` (`supported: false`, rejected fail-closed) |
| V2 | Upstream manual source pinned immutably | ✅ | `openssl/openssl` tag `openssl-4.1.0` (tag SHA `ad2c6be0...`, commit `2f4e3a5f...`), `doc/man1/openssl-x509.pod.in` (`28233` bytes, SHA-256 `794aeccc...`), `-enddate` lines `273–276` (SHA-256 `37a73615...`) |
| V3 | Valid and expired PEM expiry observations | ✅ | Valid EC P-256 cert: exit `0`, `Dec 31 23:59:59 2027 GMT` → `2027-12-31T23:59:59Z`; expired cert: exit `0`, `Jan  1 00:00:00 2021 GMT` → `2021-01-01T00:00:00Z`; both `assertion: none` |
| V4 | Negative, malformed, private-key, and size-cap cases | ✅ | Missing file (exit `1`), malformed PEM (exit `1`), private key PEM (exit `1`, `private_key_bytes_leaked: false`), oversized file rejected at `65536`-byte cap |
| V5 | Shell-injection immunity | ✅ | Filename `my 'cert' $(touch pwned).pem` invoked via direct argv (`shell=False`): exit `0`, valid UTC `2027-12-31T23:59:59Z`, `sentinel_created: false` |
| V6 | Selective drift scoping | ✅ | Mutating `-modulus` affects only `key.modulus_legacy`; mutating `-enddate` selects `certificate.expiry` |

## Pinned versions and provenance

- **Tested OpenSSL binary:** `/opt/homebrew/bin/openssl` — `OpenSSL 4.1.0 23 Oct 2026 (Library: OpenSSL 4.1.0 23 Oct 2026)`, `platform: darwin64-arm64-cc`, `OPENSSLDIR: /opt/homebrew/etc/openssl@4`, `MODULESDIR: /opt/homebrew/Cellar/openssl@4/4.1.0/lib/ossl-modules`
- **Tested unsupported binary:** `/usr/bin/openssl` — `LibreSSL 3.3.6`, `OPENSSLDIR: /private/etc/ssl`
- **Upstream manual source:** `https://raw.githubusercontent.com/openssl/openssl/openssl-4.1.0/doc/man1/openssl-x509.pod.in`
  - Annotated tag: `refs/tags/openssl-4.1.0` (`ad2c6be0cf142e3d8ce78fcdb25070674d28130f`)
  - Dereferenced commit: `2f4e3a5fec05394068cfa7379890ab552483a8a6` (`2026-10-23T12:16:44Z`)
  - Raw file SHA-256: `794aeccccb5b9b60fd36fd4a96b187ca7e24a9ae00714913eab27480efae950c` (`28233` bytes)
  - `-enddate` section (lines 273–276) SHA-256: `37a73615eb77f87df23c4b49e24cfea8bf29451cbd7ba4675b3c9c53ae9ae461`

## Findings (fed back into `DESIGN.md` and `docs/design/companion.md`)

1. **Host OpenSSL drifted on this machine between 2026-11-07 and 2026-11-24.** Homebrew updated
   `/opt/homebrew/bin/openssl` from OpenSSL `3.6.4` to **`4.1.0`**, while Python still links
   `OpenSSL 3.6.4` and `curl` still links `SecureTransport + LibreSSL 3.3.6`. `pkg/bridge` must
   probe the selected executable's exact `version -a` and resolved binary identity before planning
   every run, never assuming a previously seen host version.
2. **OpenSSL pads single-digit days in `notAfter=` with a leading space.** Observed on the expired
   fixture: `notAfter=Jan  1 00:00:00 2021 GMT` (two spaces between `Jan` and `1`). Go's parser in
   `M6.2` must use layout `"Jan _2 15:04:05 2006 MST"` (not `"Jan 2 ..."`), require UTC/GMT, and
   treat any unrecognized format as an explicit parse error rather than a zero time.
3. **Environment scrubbing is required for deterministic invocation.** `OPENSSL_CONF`,
   `OPENSSL_MODULES`, and `OPENSSL_ENGINES` can alter provider loading and behavior silently.
   `pkg/bridge` records whether any `OPENSSL_*` override is present in `provenance.env_overrides`
   (names only, never secret values) and passes an explicit minimal environment unless an override
   is declared.
4. **POSIX filename test coverage.** A filename containing spaces, quotes, and `$(touch pwned)` is
   legal on macOS/Linux (provided it contains no `/`) and proves direct `exec.CommandContext` argv
   invocation never invokes a shell.
5. **Private keys fail safely with exit `1`, but pre-checks still matter.** `openssl x509 -in
   disposable.key -noout -enddate` exited `1` with zero key body bytes in stdout/stderr, and `M6.2`
   must additionally pre-check PEM headers to reject `PRIVATE KEY` blocks before spawning OpenSSL.

## Frozen resource limits for `M6`

| Limit | Value | Rationale |
|---|---|---|
| `max_input_bytes` | `65536` (64 KiB) | More than enough for a single X.509 PEM certificate; rejects bundles/blobs before exec |
| `max_stdout_bytes` | `4096` (4 KiB) | `notAfter=...` is ~35 bytes; cap prevents unbounded memory allocation |
| `max_stderr_bytes` | `8192` (8 KiB) | Captures OpenSSL diagnostic lines without unbounded error dumps |
| `exec_timeout` | `5s` | Local `openssl x509 -noout -enddate` completes in <50ms; 5s fails stuck processes cleanly |
| `max_reference_fetch_bytes` | `262144` (256 KiB) | `openssl-x509.pod.in` is 28 KiB; 256 KiB caps any single man1 POD fetch |

## Frozen `companion-result/v1` contract target (one-way door)

```json
{
  "schema": "companion-result/v1",
  "execution": { "status": "completed|error|not_run", "exit_code": 0, "error_kind": "" },
  "observation": {
    "kind": "certificate_expiry",
    "raw_not_after": "Dec 31 23:59:59 2027 GMT",
    "not_after_utc": "2027-12-31T23:59:59Z"
  },
  "assertion": "none",
  "provenance": {
    "task_id": "certificate.expiry",
    "binary_path": "/opt/homebrew/bin/openssl",
    "binary_sha256": "<sha256>",
    "implementation": "OpenSSL",
    "version": "4.1.0",
    "argv": ["/opt/homebrew/bin/openssl", "x509", "-in", "<path>", "-noout", "-enddate"],
    "input_sha256": "<sha256>",
    "plan_sha256": "<sha256>"
  },
  "reference_status": {
    "supported": true,
    "source_commit": "2f4e3a5fec05394068cfa7379890ab552483a8a6",
    "source_sha256": "794aeccccb5b9b60fd36fd4a96b187ca7e24a9ae00714913eab27480efae950c",
    "section_locator": "openssl-x509:-enddate",
    "section_sha256": "37a73615eb77f87df23c4b49e24cfea8bf29451cbd7ba4675b3c9c53ae9ae461",
    "offline": true,
    "last_checked_utc": "2026-11-24T00:00:00Z"
  }
}
```

## Run

```sh
python3 -B hack/spike/m6.py
```

## Next (`M6.1` → `M6.4`)

Implement `pkg/reference` (`M6.1`) and `pkg/bridge` (`M6.2`) against the frozen limits and schema
above, satisfy every `TestM6_*` row in [docs/design/companion.md](design/companion.md#7-acceptance-matrix-binding-test-rows),
and replace `hack/acceptance/m6.sh` with the real e2e gate (`M6.4`).
