## Description

Creating custom rejection types in warp currently requires implementing several HTTP-specific trait methods — including methods for determining status codes and generating HTTP responses. This is overly burdensome for types that are purely domain-specific; they shouldn't need to know anything about HTTP just to represent a custom failure condition.

## Expected Behavior

- Custom rejection types should only need to derive debug output and implement a lightweight marker trait with no required methods.
- A minimal type definition followed by a one-line marker trait implementation should be enough to use a type as a custom rejection.
- The rejection constructor should accept any type implementing this marker trait directly, without needing to wrap it in a boxed error object or implement the standard error trait.
- Rejection values should expose a type-safe lookup method that returns a reference to the stored custom value when queried with the correct type.

## Why This Matters

The current design forces custom rejection types to be aware of HTTP-level concerns (status codes, response generation) at the point of definition, rather than at the point of recovery. This couples error modeling to HTTP rendering and makes the API more verbose than necessary. The new approach keeps custom types simple and lets recovery handlers decide how to translate them into HTTP responses.
