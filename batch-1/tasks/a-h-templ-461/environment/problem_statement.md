## Description

The templ parser needs several improvements to handle more advanced Go expression patterns within template syntax. Currently, developers cannot use slice-indexed or map-indexed component references in template elements (e.g., selecting a component from a slice or map by index), which prevents dynamic component selection patterns. Template parameter lists also cannot span multiple lines, making complex signatures awkward to write. String expressions inside templates cannot span multiple lines either. Additionally, there is no dedicated parsing subsystem for extracting the Go expression portions of control-flow statements (conditionals, loops, switch expressions, case clauses) from template source.

## Expected Behavior

- Template element expressions should support referencing components stored in slices (by integer index) and maps (by string key), and calling methods on those values.
- Template definitions should allow their parameter lists to span multiple lines.
- String expressions within template syntax should support being written across multiple lines.
- A new expression-extraction subsystem should be available for parsing Go control-flow expressions (if conditions, for loop expressions, switch expressions, case/default clauses) and general template call expressions, returning the extracted expression boundaries within the source string.
- A utility to extract argument list content from a source string should be available, returning the argument string directly.
- When an unclosed statement or comment is encountered, the reported error position should consistently indicate the start of the input rather than pointing to an end-of-input position.

## Why This Matters

These improvements let developers write more expressive and idiomatic templates, including dynamic component selection via slices and maps. The new expression-extraction subsystem enables more accurate, context-aware processing of template source, which is foundational for correct formatting, tooling, and code generation. Consistent error position reporting reduces confusion when diagnosing parse failures.
