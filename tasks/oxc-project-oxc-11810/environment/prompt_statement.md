I'm working on a JavaScript minifier written in Rust and running into a compilation error that's blocking the entire test suite. The issue seems to be with some core traits that handle checking whether identifier references are global variables, and another trait that provides context for side-effect analysis.

These traits currently have no lifetime parameters, but the AST node types they receive in their methods carry a lifetime. Because the lifetime is not propagated through the trait definition itself, the compiler can't verify that the identifier reference objects live long enough. I need to add explicit lifetime parameters to these traits and update all their implementations (including in the test files) so that the relationship between the AST lifetime and the identifier references is properly expressed.

Once fixed, the minifier crate should compile and all its tests should pass — right now nothing can run because of this compilation failure.
