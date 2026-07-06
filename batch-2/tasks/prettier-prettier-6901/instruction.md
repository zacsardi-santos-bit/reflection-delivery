Implement consistent line-wrapping behavior for complex TypeScript type expressions within generic type angle brackets when they exceed the configured line width. Ensure that these types are formatted correctly in both variable declarations and arrow function return type annotations.

*   Ensure that when a TypeScript generic type parameter contains:
    *   A union type (using the | operator) and exceeds the print width, it is broken onto its own indented line, with the closing angle bracket on a new line.
    *   An intersection type (using the & operator) and exceeds the print width, it is broken onto its own indented line, with the closing angle bracket on a new line.
    *   A 'keyof' type expression and exceeds the print width, it is broken onto its own indented line, with the closing angle bracket on a new line.
    *   An array type (using [] suffix) and exceeds the print width, it is broken onto its own indented line, with the closing angle bracket on a new line.
    *   An indexed access type (using ["key"] notation) and exceeds the print width, it is broken onto its own indented line, with the closing angle bracket on a new line.
*   Maintain inline formatting for TypeScript generic type parameters that fit within the configured print width.
*   Apply the wrapping behavior consistently in both variable declarations and async arrow function return type annotations.
*   Format the type argument on its own indented line immediately after the opening angle bracket, with the closing angle bracket on its own line at the original indentation level.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.