## Description

Filtering through interface-typed relationships in GraphQL queries returns incorrect or broken results. When a type has a relationship that points to an interface (rather than a concrete type), and a query includes a filter on properties of the related interface-implementing nodes, the generated database queries have naming conflicts that cause incorrect behavior.

## Expected Behavior

- When filtering a list of nodes by a property on a related entity that implements an interface, results should be returned for any concrete implementation that satisfies the filter.
- If the matching node is of the first concrete implementation type, it should be returned correctly.
- If the matching node is of the second concrete implementation type, it should also be returned correctly.
- If no related node satisfies the filter across any concrete type, the result should be empty.
- This should work for both single (non-list) interface relationships and list interface relationships (using existence quantifiers).

## Why This Matters

Many schemas model polymorphic relationships as interfaces. If developers can't filter by interface properties, they lose a fundamental querying capability. Queries that look correct based on the schema silently return wrong data, making it extremely difficult to debug. This is caused by internal variable name collisions in the generated database queries when multiple optional match count patterns reuse the same hardcoded name derived from the relationship name.
