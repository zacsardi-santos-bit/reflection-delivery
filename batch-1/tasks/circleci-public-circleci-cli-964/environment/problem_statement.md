## Description

When using the policy evaluation commands in the CircleCI CLI, the config file provided for evaluation is always used in its raw, uncompiled form. This means policies are evaluated against the source configuration before orbs are expanded and other compilation transformations are applied. In production, CircleCI runs pipelines against the *compiled* version of a config — so evaluating policies against the source config can give misleading results.

We need the policy evaluation commands to optionally compile the config through the CircleCI API before passing it to the policy decision engine. The compiled config should be appended alongside the source config so that policies can reason about both forms. Users who prefer to evaluate against the source config should be able to opt out of compilation.

## Expected Behavior

- By default, the policy evaluation and raw evaluation commands should compile the input config via the CircleCI API before sending it for policy evaluation. The compiled version should be embedded under a designated sentinel key alongside the original source config.
- If the source config already contains that sentinel key at the top level, the compiled output should have that key stripped before embedding (to avoid conflation).
- A flag should be available on both commands to opt out of compilation and evaluate against the raw source config only.
- When compilation is enabled and a local policy path is provided, the owner ID must be specified (since compilation requires an API call with org context). A clear error message should guide users to the opt-out flag if they don't need compilation.
- The restriction preventing use of a local policy path together with an owner ID should be removed.

## Why This Matters

Policy decisions made against the uncompiled config may not reflect what CircleCI actually executes. By compiling first, developers get accurate policy evaluation results that match the effective pipeline configuration, reducing false passes or failures during policy validation.
