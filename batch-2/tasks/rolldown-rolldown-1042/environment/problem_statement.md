## Description

The testing library for this bundler project currently embeds its configuration file loading logic directly on the configuration data type as an associated function. This design makes it hard to add preprocessing steps (like schema validation or richer error reporting) without modifying the core data type, and it scatters responsibilities across crates in a way that is difficult to maintain.

## Expected Behavior

- The configuration loading functionality should be moved into a dedicated submodule within the testing library.
- The submodule should expose both the configuration type and a standalone function for reading and parsing configuration files from a given file path.
- The standalone function should replace the existing approach (calling a method directly on the configuration type), providing at minimum the same behavior for all existing test configurations.

## Why This Matters

Reorganizing this responsibility into its own module keeps the configuration data type focused on structure and deserialization, while allowing the loading routine to grow independently. Future additions — such as validation against a schema — can be added to the loading function without affecting the data type itself, improving long-term maintainability.
