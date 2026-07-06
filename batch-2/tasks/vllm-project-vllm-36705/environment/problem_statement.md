## Description

The Helion kernel registration workflow has two usability problems that should be fixed together.

**Two-step registration is fragile.** Currently, registering a kernel requires creating a wrapper first and then attaching a configuration picker afterward as a separate call. This means a wrapper can exist in a half-initialized state between those two steps, and it's easy to forget the second step entirely. The configuration picker should be a required argument at registration time so the wrapper is always complete when it's created.

**Missing configs cause a runtime crash.** When a kernel is registered but no pre-tuned configurations exist for the current hardware platform, the failure only surfaces when the kernel is actually called — not at setup time. This makes it impossible to handle unsupported hardware gracefully or to run autotuning to generate missing configurations. Instead, the wrapper should detect missing configs eagerly at construction and mark itself as disabled with a clear explanation, raising a descriptive error if called directly.

## Expected Behavior

- The configuration picker must be provided at registration time, not separately afterward.
- All GPU detection and config loading must happen at construction time, not on the first call.
- A wrapper with no available platform configurations must be marked as disabled at construction, storing a reason string that explains why.
- Calling a disabled wrapper directly must raise a clear error indicating it is disabled.
- Disabled wrappers must still support autotuning workflows so configurations can be generated for unsupported hardware.
- Disabled wrappers must remain visible in the global kernel registry.

## Why This Matters

This makes kernel setup failures visible immediately at startup rather than unexpectedly at inference time, enables autotuning as a path to support new hardware, and eliminates a class of bugs caused by incomplete two-step registration.
