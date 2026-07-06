## Description

The join directive currently only supports joining with a single top-level query field. It cannot traverse multiple levels of the type graph in a single join declaration. This means that if the target data lives behind an intermediate resolver type, developers cannot access it via a join.

## Expected Behavior

- The join directive should accept a selection path that spans multiple type levels — selecting through an intermediate type before reaching the final target field.
- Variables from the parent object (the type that declares the join) should be usable as arguments at any level of the nested path.
- Literal argument values should also be supported alongside variable references in the nested path.
- The resolver chain should be invoked correctly at each level, with the result of each step serving as the parent context for the next.
- The final value returned should be the scalar or object produced by the terminal resolver in the chain.

## Why This Matters

Without nested join support, developers must write custom resolver logic to compose data that requires passing through intermediate types. Supporting multi-level selection paths in joins makes it much easier to link data across a chain of resolvers in a declarative way, reducing boilerplate and keeping schema definitions clean.
