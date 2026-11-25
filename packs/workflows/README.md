# Workflow pack boundary

First task: `certificate.expiry`, a read-only observation of one PEM certificate's notAfter date.
The fixed command recipe, typed inputs, provenance and error cases are defined in
[companion design §4](../../docs/design/companion.md#4-workflow-and-execution-contract).

No runnable task manifest or parser exists yet. M6.2 implements them with positive, expired,
malformed/missing input, process-error and literal-path fixtures. Task definitions must cite exact
reference sections and name their test dependencies. An observation is not a trust verdict.
