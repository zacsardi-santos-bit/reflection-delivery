## Description

The floating promise detection lint rule fails to catch cases where a method on an object returns an asynchronous value without an explicit return type annotation. This is a common real-world pattern: a developer writes a method whose body clearly returns an asynchronous value (e.g., by calling an API that returns such a value), but the return type is inferred, not declared. Currently, the linter misses these cases entirely, meaning unhandled asynchronous return values can silently slip through.

The root cause is that the type inference system falls back to an indeterminate return type whenever a function or method lacks an explicit return type annotation, rather than inferring the type from what the function actually returns. This affects not just floating promise detection but the entire type system's ability to reason about function return values.

## Expected Behavior

- When a function or method body can be statically analyzed to determine its return type, that type should be inferred rather than left as indeterminate.
- Functions that never return a value should have their return type inferred as an empty/no-value return type.
- Functions with multiple return paths should have their return type inferred as a union of all possible return types, with duplicate types deduplicated.
- The floating promise lint rule should flag calls to unannotated methods whose inferred return type is an asynchronous value, just as it would for annotated ones.

## Why This Matters

Many real-world codebases rely heavily on type inference rather than explicit annotations. If linters and type tools only work for explicitly typed code, they provide much weaker guarantees. This fix ensures that return type inference covers function bodies, making the floating promise rule (and other type-dependent rules) effective for idiomatic TypeScript code that omits redundant return type annotations.
