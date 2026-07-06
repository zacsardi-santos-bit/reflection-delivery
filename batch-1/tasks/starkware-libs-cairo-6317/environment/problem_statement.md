## Description

Cairo supports two documentation comment styles: outer doc comments placed before an item, and inner doc comments placed inside an item's body. Currently, the documentation extraction system only reads the outer-style comments. Inner comments inside function bodies or module bodies are silently ignored. Additionally, the system does not capture file-level comments at the top of crate or submodule files, meaning crate-level and module-level documentation is never surfaced.

## Expected Behavior

- Documentation for functions, trait methods, and impl methods should combine both the outer comments and any inner comments found inside the body, presented together as one documentation string.
- Documentation for inline modules should combine the outer prefix comment with any inner comments in the module body.
- Documentation for non-inline (file-based) submodules should include the file-level comments from the module's source file, along with the outer declaration comment.
- Documentation for crates should return the file-level comments from the root source file.
- IDE hover tooltips in the language server should show the full combined documentation (both outer and inner comments) for any item that has them.
- Items with only outer comments (structs, enums, traits, impls, members, variants) should continue to return their outer documentation unchanged.

## Why This Matters

Developers writing Cairo code often use inner comments to document what a function does from the inside — a valid and idiomatic pattern. These comments are currently invisible to tooling, so users hovering over a symbol in an IDE see incomplete documentation. Fixing this ensures all documentation is captured regardless of which comment style the author chose.
