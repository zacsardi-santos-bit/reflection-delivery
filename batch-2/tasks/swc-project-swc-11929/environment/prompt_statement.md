I'm hitting a parser bug in the SWC Flow type support. Flow lets you annotate that something renders a specific component type using a rendering annotation keyword followed directly by a type reference, the bare form (just the keyword and the type, no extra prefix character), and it looks like we don't handle that yet. It's common in React codebases so I keep running into it.

Right now when the parser sees this bare renders type syntax in any type position it just fails instead of producing the right AST. What I want is for the keyword to get consumed and discarded, with the result being the underlying type reference showing up in the AST, no special wrapper node for the keyword itself. So parsing succeeds and the tree just contains the referenced type.

There's also a nullable variant where a question mark sits between the keyword and the type reference, and that one should parse as a union type that includes the referenced type along with the two nullability primitives (null and void).

This needs to work across all the type positions: type alias declarations, function parameter type annotations, object/type literal property type annotations, arrow function return type annotations, and generic type arguments. Oh and it also needs to work in component declaration return type annotations when components support is enabled.

Basically a bunch of real Flow-typed React code uses this to express component render contracts and we can't parse it today, so could you fix the Flow parser to support the bare renders type syntax everywhere it can legally appear? The Flow parsing logic lives under the SWC parser crate (`@crates/swc_ecma_parser/src`) where the other Flow type-position handling already is.
