# Current quickstart and limitations

Status: scaffold, checked 2026-11-12. The `ciphertls-core` entry point is a placeholder. The product
commands illustrated in the README and design are targets, not a usable release.

## What can be checked today

Install Go matching `go.mod` and Python 3.9 or newer, clone this repository and run:

```sh
sh hack/ci.sh
python3 hack/acceptance.py --phase M6
```

The first command checks the development baseline. The second must fail while M6 remains a
skeleton. Its failure is an honest availability signal; it is not an installation fix.

## First usable path being built

Select the existing OpenSSL executable explicitly, resolve an applicable reference pack, and
request `certificate.expiry` for a disposable local PEM certificate. Review the argument list,
execute explicitly, and receive its expiry plus the matching source. This is an observation,
not certificate trust validation. Offline lookup retains its snapshot and shows freshness.

M6 must work without NL or an external source service. M7 evaluates optional natural-language
task selection after that path works. Product commands and install artifacts are not available yet.

See [design](../DESIGN.md), [phases](../PHASES.md) and [setup review](../setup-review.md).
The release must provide exact dependencies, installation, expected outputs and a tested clean
checkout before this becomes a runnable tutorial.

## Limits

No completed M6, released SDK/pack, working product or user validation is claimed. Exact supported
builds and source mapping must be established in core M6.0. Later chain, container and workbench
scope retains its existing phase IDs. See the companion design and DR-008 in core.
