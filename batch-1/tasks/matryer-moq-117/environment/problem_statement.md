## Description

The mock generator constructor currently takes two separate positional string arguments (source directory and package name). This design makes it difficult to add new configuration options in the future without breaking all existing callers. We should refactor it to accept a single configuration struct instead.

## Expected Behavior

- The constructor should accept a configuration struct with named fields for source directory, package name, and formatter choice
- Callers only need to specify the fields they care about; unused fields fall back to sensible defaults
- The source directory field is the only required setting; omitting the package name should cause it to be inferred automatically
- A new formatter option should be supported, allowing users to choose between the standard Go formatter and an import-aware formatter
- When no formatter is specified, the standard Go formatter should be used as the default

## Why This Matters

As the tool grows, adding new options via positional arguments becomes unwieldy and fragile. A configuration struct makes the API more ergonomic, self-documenting, and extensible. It also enables the addition of a formatter selection option, which allows generated mock code to be post-processed with a smarter import-organizing formatter if desired.
