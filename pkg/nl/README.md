# Optional NL adapter boundary

Planned M7 task-selection interface. Keep its runtime/build optional and its output limited to
the contract in [companion design §5](../../docs/design/companion.md#5-nl-contract-and-baseline).
The core bridge validates proposals; no NL call alone causes execution. There is no implementation
or selected model dependency in this directory today.
