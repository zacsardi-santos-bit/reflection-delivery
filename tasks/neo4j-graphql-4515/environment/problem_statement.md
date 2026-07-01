## Description

When a GraphQL interface type defines a relationship to another node type, the auto-generated schema is missing important querying capabilities on those relationship fields. Specifically, the generated interface definition lacks arguments for filtering, pagination, and traversal direction — arguments that are correctly generated for the same relationships on concrete implementing types.

## Expected Behavior

- When an interface includes a relationship field, that field in the generated schema should expose arguments for filtering related nodes, specifying traversal options, and controlling direction.
- The corresponding connection field for that relationship should also include arguments for pagination (first/after), sorting, filtering, and direction.
- In the extended schema mode, the interface's filter input type should include full relationship-level filter options — including filtering for all, none, single, or some related nodes matching a condition — as well as aggregate-level filters.
- In the extended schema mode, the appropriate aggregation input types for the relationship should be generated for interface types, just as they are for concrete types.
- These behaviors should apply consistently regardless of whether the interface has additional custom directives applied to it.

## Why This Matters

Developers using interface types with relationships end up with a schema that doesn't let them filter or paginate related nodes through the interface. This creates an inconsistency: the same relationship works correctly when accessed through a concrete type, but lacks arguments when accessed via the interface. This forces workarounds and creates an incomplete API surface that undermines the purpose of using interfaces in the schema.
