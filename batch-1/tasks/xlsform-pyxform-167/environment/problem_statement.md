## Description

The XLSForm conversion tool currently only supports a single validator. We need to add support for a second validator that can be run in addition to or instead of the existing one, with proper command-line controls to choose which validators to run.

Additionally, the error-cleaning logic that processes validator output currently has two bugs:
1. Single-line error messages are silently dropped instead of being included in the output.
2. Errors that contain file system paths (such as a missing validator jar file) are incorrectly broken apart, mangling the path information.

## Expected Behavior

- The command-line tool should support a new flag to enable the second (Enketo-based) validator, which defaults to off.
- A flag to skip all validation should disable all validators regardless of other flags, taking priority over any explicit validator-enable flags.
- When neither the skip flag nor explicit validator flags are used, only the original validator runs by default.
- The error-cleaning utility should be moved into a dedicated validators module.
- Single-line error messages must be preserved in the cleaned output.
- Errors that refer to missing validator files must be returned without modification, preserving the full path.

## Why This Matters

Users need flexibility to choose which validation tools are applied to their forms. The existing bugs in error reporting mean that some errors are silently lost or their messages are corrupted, making it harder to diagnose form problems.
