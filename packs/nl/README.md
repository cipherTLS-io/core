# Optional NL package boundary

M7 evaluates language-to-task routing. No model, training corpus or runtime dependency is selected.
The base companion must install and run without this package. Keep training and held-out tests
separate; include negation, missing inputs and requests outside the supported task set.

The output is a task ID and proposed typed inputs, never shell code. Current manuals, commands
and release facts live in reference/workflow packs. See [companion design §5](../../docs/design/companion.md#5-nl-contract-and-baseline).
