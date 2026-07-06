## Description

When loading a model checkpoint that was originally saved as one architecture type, but the user wants to treat it as a different custom-registered architecture via a model type override, the config loading system does not correctly handle the mismatch. Specifically, the custom config class is registered under the overridden type, but not under the type stored on disk. This means that standard HuggingFace auto-config lookups — which use the on-disk type — still return the original config class rather than the custom one.

## Expected Behavior

- When a custom config class is registered and a model type override is specified at load time, the returned config object must be an instance of the custom class — even if the checkpoint's on-disk config declares a different architecture.
- The custom config class must be registered in the auto-config mapping under both the overridden type and the on-disk type.
- After such an override, any standard HuggingFace config lookup for that checkpoint directory must also return the custom config class, not the one originally associated with the stored architecture type.

## Why This Matters

Users who load checkpoints with custom config overrides expect all config resolution pathways to be consistent. Currently, the override only partially takes effect: the direct load returns the right class, but subsequent standard lookups fall back to the wrong one. This creates subtle inconsistencies when the same checkpoint is accessed via different APIs.
