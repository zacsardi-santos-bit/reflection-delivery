I'm working on a Go CLI library and I want to redesign the value-source system used by flags. Right now, each flag can have multiple sources (like environment variables or files) that it checks when looking up its value. The problem is that the current design only lets you get back the value and a plain string identifier for the source — there's no way to get a reference to the actual source object that resolved the value.

I'd like to replace the existing approach with a cleaner design. Specifically, I want a public interface that source types must satisfy, which includes human-readable and Go-syntax string representations in addition to a lookup method. I also want a chain type that holds an ordered list of these sources and supports both a basic lookup (just value and found) and an extended lookup that also returns which source in the chain actually provided the value.

The helper functions that create chains from a list of environment variable names or file paths should return this chain type directly, so they can be assigned straight to a flag's sources field. The file-path helper should also be renamed for consistency with the rest of the API.

The internal source structs for environment variables and files should be unexported, with clearly named fields. When a flag fails to parse a value obtained from an environment variable, the error message should identify the value, the expected type, the environment variable name, and the flag name.

The flag type's sources field should be updated to use the new chain type instead of the old slice type, and all existing usages in tests should be updated accordingly.
