Update the code generator to produce correctly formatted JavaScript and TypeScript code for specific statement types. Ensure class declarations, resource-management statements, and nested declarations within modules are structured with proper punctuation and indentation.

*   Ensure class declarations at the top level end with a newline:
    *   Output should append a newline character after the closing brace of a class declaration (e.g., 'class Bar {}' becomes 'class Bar {}\n').

*   Correctly format resource-management statements:
    *   Add a semicolon immediately after the statement, followed by a newline (e.g., 'using x = foo()' becomes 'using x = foo();\n').

*   Indent interface declarations within TypeScript module blocks:
    *   Indent the interface by one tab character relative to the module block.
    *   Ensure the module's closing brace is on its own line (e.g., 'module Foo { interface Bar {} }' becomes 'module Foo {\n\tinterface Bar {}\n}\n').

*   Indent class declarations within TypeScript module blocks:
    *   Indent the class by one tab character and ensure it is followed by a newline.
    *   Ensure the module's closing brace is on its own line (e.g., 'module Foo { class Bar {} }' becomes 'module Foo {\n\tclass Bar {}\n}\n').

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.