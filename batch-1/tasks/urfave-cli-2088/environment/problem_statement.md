## Description

The positional argument system in this CLI library is incomplete and inconsistent. Currently, only 64-bit integer positional argument types exist, leaving developers without native support for standard-width integer types and their unsigned variants. There is also no symmetric pair of "single value" and "multi-value" argument types — the existing base type conflates both behaviors in one struct, which leads to confusion when a developer wants to capture exactly one value versus a variable number of values.

Additionally, there is no type-safe way to retrieve a positional argument value from a command by name at runtime. Developers who have registered arguments must either carry external destination variables or deal with untyped retrieval, unlike the ergonomic accessor pattern already established for flags.

## Expected Behavior

- Positional argument types should be available for all common Go integer sizes (both signed and unsigned), not just 64-bit
- Each integer size should have both a single-value variant and a slice/multi-value variant
- The same pattern should apply to existing float, string, and timestamp argument types — each having a distinct single-value type and a distinct multi-value type
- The multi-value argument types should use a consistently named field (a pointer to a slice) for receiving parsed results, matching the naming convention already used by flags and single-value argument types in the library
- Typed accessor methods on the command should allow retrieving a positional argument's current value by name, returning the appropriate Go zero value or nil slice when the argument is not found or the type does not match

## Why This Matters

The argument system should be as complete and ergonomic as the flag system. Developers should be able to define positional arguments of any standard numeric type and retrieve them in a type-safe way, matching the same ease of use that exists for named flags.
