## Description

The modeling structure linter is missing a rule that verifies a critical relationship between model weight tying and configuration. When a model class declares that certain weights are tied (two parameters sharing the same tensor), the corresponding configuration class must expose a flag that lets users control whether that tying is active. Without this flag in the configuration, tying is unconditional — users cannot disable it, serialization may break, and fine-tuning with untied heads fails silently.

Currently there is no automated check for this constraint, so it is easy to add tied weights to a model while forgetting to add the corresponding option to the configuration class. The linter should detect this mismatch and report a clear violation pointing to the specific configuration class that needs updating.

## Expected Behavior

- When a model class in a modeling file declares a non-empty tied-weights collection, the linter checks whether the associated configuration class declares the weight-tying control flag.
- If the flag is missing, one violation is produced per affected model class, with a message that names both the missing field and the specific configuration class that should be updated.
- No violation is produced when the tied-weights collection is empty, when the configuration class inherits the flag from a parent model config, or when the model explicitly resolves to a sub-configuration that already has the flag.
- Config file matching works correctly for multi-component models that have separate configuration files per component (e.g., audio, text, vision).
- The linter's result cache must be invalidated when the configuration file changes, even if the modeling file itself is unchanged.

## Why This Matters

This prevents a class of silent misconfiguration bugs where tied weights are hardcoded into a model with no way for users to opt out, which breaks fine-tuning workflows and serialization round-trips.
