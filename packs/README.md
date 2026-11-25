# Companion packs

Selected boundaries under [DR-008](../docs/decisions/DR-008-companion-first.md).
These are implementation homes, not published/installable packs yet.

- [reference](reference/README.md): source bytes, notices, immutable identities and a derived index.
- [workflows](workflows/README.md): reviewed tasks and their reference/fixture dependencies.
- [nl](nl/README.md): optional model/router artifacts and separate training/evaluation material.

Core M6 implements reference/workflow support. M7 evaluates the optional NL package. Consumers
must identify the exact pack and compatible API revision; a directory name is not a release pin.
