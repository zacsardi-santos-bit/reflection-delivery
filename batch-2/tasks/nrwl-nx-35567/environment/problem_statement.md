## Description

The existing monolithic NgRx generator for Angular workspaces handles both root store setup and feature state generation through a single entry point, relying on a toggle flag to differentiate the two use cases. This design has led to a confusing mix of options that are only relevant to one scenario or the other. The plan is to split this into two dedicated generators — one for root store setup, one for feature state generation.

However, many teams already have saved generator preferences (defaults) in their workspace configuration files or per-project configurations. These saved defaults reference the old generator name. After the split, those preferences would silently stop applying, potentially causing unexpected behavior or requiring manual intervention.

## Expected Behavior

- A migration should automatically update saved generator defaults to point to the correct new generator.
- Defaults should be redistributed according to which options are relevant to root store setup vs. feature state generation.
- The migration should handle both the flat configuration format and the nested format that some workspaces use.
- A deprecated option for specifying the parent module path should be normalized to the new option name.
- Obsolete options (like the root/feature toggle itself) should be dropped; if dropping them leaves nothing to configure, no empty entries should be written.
- Existing user customizations under the new generator keys should not be silently overwritten — the migration should merge rather than replace.
- The migration should process both the workspace-level configuration and all per-project configuration files in a single run.
- Projects or workspaces with no relevant defaults should be left completely unchanged.

## Why This Matters

Without this migration, teams upgrading to the new version would find that any generator preferences they had configured simply stop working. The migration ensures a smooth, zero-friction upgrade path that respects existing configurations.
