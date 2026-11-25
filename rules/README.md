# Rule authoring

Status: draft M0 schema; no rule parser or execution engine exists yet.

Reusable verification lives here. Products reference a rule ID and bind named artifact inputs;
they do not duplicate verification commands. See [the chain rule](correctness/chain.yaml) and
[the shared evidence contract](../docs/design/evidence-contract.md).

A check declares an `argv` vector with named input placeholders, an expected cryptographic
outcome and, for an invalid outcome, its specific reason. The runner resolves paths within the
allowed input mount and passes arguments without constructing a shell command from user input.
Missing inputs, invalid bindings, timeouts and runtime errors cannot satisfy a negative assertion.

The rule's release aliases resolve to exact recorded versions before execution. Each result
records the source, image, rule and input identities and explicit trust/time/configuration context.
Author tests must distinguish complete chain, intended invalid chain and unrelated execution error.
The future parser must reject unknown fields and unresolved placeholders.

The workshop's reference solution is author-validation data. Actual assessment uses the learner's
submission. The terminal interprets the observed outcome; it does not reuse learner pass/fail as
an operator diagnosis. New policy sets remain later roadmap work.
