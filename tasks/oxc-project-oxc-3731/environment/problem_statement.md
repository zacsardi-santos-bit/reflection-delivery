## Description

The code generator is producing malformed output for several JavaScript and TypeScript statement types. Specifically:

- **Class declarations** are emitted without a trailing newline, so they run together with whatever follows them in the output.
- **Resource-management statements** (the newer syntax for declaring objects with automatic cleanup) are being generated without a terminating semicolon or trailing newline, making the output syntactically invalid.
- **Interface declarations** and **class declarations** that are nested inside TypeScript namespace/module blocks are not being indented — they are output at the leftmost column instead of being indented to reflect their nesting level.

## Expected Behavior

- A standalone class declaration should produce output ending with a newline.
- A resource-management statement should produce output with a semicolon and trailing newline.
- An interface declaration nested inside a module block should be indented by one level (tab character) within the module's braces, with the closing brace on its own line.
- A class declaration nested inside a module block should likewise be indented by one level (tab character), with the module's closing brace on its own line.

## Why This Matters

The generated code is not valid or correctly formatted in these cases. Any downstream consumer of the generated output — formatters, compilers, or developers reading it — will encounter incorrectly structured code that either lacks required punctuation or ignores nesting indentation.
