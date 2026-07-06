## Description

When saving a PEFT model that has multiple adapters loaded and specifying a subset of adapter names to save, the save operation incorrectly includes weights from unintended adapters if the specified adapter name appears as a substring in another adapter's name (or vice versa).

For example, suppose a model has adapters named "default", "default2", "other_default", "foodefault_bar", and "efaul" all loaded simultaneously. When saving only the "default" adapter, the saved checkpoint ends up containing more weights than it should — picking up entries from adapters whose names partially overlap with "default" through substring relationships.

## Expected Behavior

- Saving a model with a specific adapter name selected should produce a checkpoint file containing only the weights that belong to that exact adapter.
- The number of saved weights should match exactly what that single adapter contributes, regardless of whether other loaded adapters have names that contain the target name as a prefix, suffix, infix, or substring.

## Why This Matters

Users who manage multiple adapters on a single base model and rely on named saves to produce clean, isolated checkpoints currently get oversized or contaminated checkpoint files whenever adapter names have substring overlap. This makes it impossible to reliably checkpoint and restore individual adapters in multi-adapter workflows.
