## Description

The vertex base class in the graph engine is doing too much work. The logic for reading, converting, and routing template parameters (handling strings, numbers, code snippets, file references, boolean values, tabular data, etc.) is all embedded inline in a single long method. This makes the code hard to maintain and impossible to unit-test in isolation.

We should extract this parameter-processing logic into a dedicated, standalone class that can be instantiated and tested independently of the full vertex infrastructure. The class should own all the rules for how different field types are converted (e.g. how file paths are resolved, how code fields are evaluated, how dict fields are merged, how table fields become dataframes), and expose clean, individually testable methods for each concern.

In addition, one of the model component modules should be renamed to better reflect its contents, and all references to that module's location should be updated.

## Expected Behavior

- A dedicated parameter handler class exists and is accessible from the vertex base module
- The handler can be constructed with a vertex and a storage service
- It provides separate methods for: processing edge-connected parameters, processing individual file fields, determining whether a field should be skipped, processing non-list edge parameters, handling optional fields with defaults, and processing all template field parameters at once
- Processing all template fields returns a parameter dict and a list of fields to load from the database
- Unknown field types raise a descriptive error
- Invalid table field values raise a descriptive error
- Code field evaluation failures fall back gracefully to the original value
- The renamed model component module exports the same component class as before

## Why This Matters

Separating this logic makes it easy to write targeted unit tests for each piece of parameter handling behavior, and makes the vertex base class itself simpler and easier to reason about.
