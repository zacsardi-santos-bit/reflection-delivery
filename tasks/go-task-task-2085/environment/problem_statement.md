## Description

Currently, creating an executor requires callers to directly initialize a configuration object with every field they want to set. This means anyone constructing an executor must know the exact internal field names and types, creating tight coupling between callers and the executor's internal representation. There is also no way to set sensible defaults automatically — every field that matters must be spelled out explicitly.

Additionally, enabling verbose logging currently requires importing and constructing a separate internal logger object and assigning it as a field on the executor. This is cumbersome and exposes an internal implementation detail to external callers.

## Expected Behavior

- The library should expose a constructor function that accepts a variadic list of option functions, creates an executor with sensible defaults, and applies the provided options.
- Each configuration setting (working directory, I/O streams, temp directory, force flags, network options, output style, concurrency, etc.) should have its own named option function, following the same naming convention used elsewhere in the codebase.
- Enabling verbose output should be possible directly through one of these option functions, without needing to import or configure a separate internal logger object.
- After construction via the new function, the executor should behave identically to one created through direct field assignment — all existing methods must continue to work.
- A small number of fields (such as terminal simulation and user working directory overrides) may still be settable directly on the returned value for cases where no option function is needed.

## Why This Matters

This change encapsulates the executor's configuration behind a stable API, making it easier to add new fields or change defaults in the future without breaking all existing call sites. It also makes executor initialization much more readable, since callers only need to specify the options relevant to their use case rather than listing all fields including unneeded ones.
