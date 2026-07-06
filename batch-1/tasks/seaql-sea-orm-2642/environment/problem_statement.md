## Description

When building custom query result types that include data from related entities, it is currently only possible to use purpose-built partial model types as nested fields. If you want to include all columns from a related entity in your query result, you still have to create a separate intermediate struct — even though the full entity model already contains exactly those fields.

Similarly, if you want to query a table and receive results typed as the entity's own model using the partial model query API, this is not currently supported. You must define a redundant wrapper type.

## Expected Behavior

- A full entity model type should be usable directly as the target of a partial-model query, without needing a separate wrapper struct.
- A full entity model type should be usable as a nested field (wrapped in an optional container) within a partial model struct, allowing left joins to populate the complete related record or return an absent value when no match exists.

## Why This Matters

This eliminates boilerplate when the full record of a related entity is needed. Developers should not have to duplicate field definitions just to get the behavior that the full entity model already describes. Making entity models first-class citizens of the partial model system makes the API more ergonomic and consistent.
