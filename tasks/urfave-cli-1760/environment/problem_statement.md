## Description

The current value-source API for CLI flags is inconsistent and limited. The existing design uses a simple interface with basic lookup and identifier methods, backed by a plain slice type. This makes it impossible to determine *which* specific source within a chain actually provided a flag's value — you only get back the value and a plain string label, not a reference to the source itself.

Additionally, the file-path source helper function has an inconsistent name compared to the env-var helper, and the underlying source types are public when they don't need to be, leaking internal implementation details into the public API.

## Expected Behavior

- A redesigned value-source interface that includes human-readable and debug-friendly string representations, along with a lookup method that returns both the value and whether it was found.
- A chain type with an ordered list of value sources that supports both a simple lookup (returning value and found indicator) and an extended lookup that also returns the specific source that resolved the value.
- The environment-variable helper and file-path helper should both return this chain type directly, so they can be assigned to a flag's sources field without wrapping.
- The file-path helper should be renamed for API consistency.
- The internal source types for environment variables and files should be unexported structs with clearly named fields, rather than public string-based types.
- The flag sources field type should be updated from the old slice type to the new chain struct.

## Why This Matters

This redesign makes it easy to trace exactly where a flag's value came from when multiple sources are configured (e.g., an env var or a file). It also produces clearer debug output through proper debug-string formatting and improves the overall API consistency of the library.
