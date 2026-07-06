## Description

The SWC HTML code generator and minifier produce incorrect output when they encounter HTML where a block-level element is nested directly inside an inline container element. This kind of markup is technically invalid per the HTML specification, but browsers perform error recovery to handle it gracefully. SWC's HTML parser similarly recovers from this, but the code generator and minifier downstream fail to handle the recovered structure correctly.

## Expected Behavior

- When regenerating such a document in normal mode, the full document structure should be emitted correctly with the nested element structure preserved inside the body.
- When regenerating in minified mode, the nested structure should be preserved exactly as it appeared in the input — no element nesting should be altered or collapsed.
- The HTML minifier's error-recovery path for elements should likewise preserve the nested structure unchanged.

## Current Behavior

The HTML code generator and minifier corrupt or incorrectly transform the element structure when a block-level element appears as a child of an inline element, resulting in output that does not match the parsed structure.

## Why This Matters

Real-world HTML documents frequently contain invalid markup that browsers recover from. SWC's HTML toolchain must handle such markup correctly and round-trip it faithfully, especially during minification where incorrect structural changes could silently break pages.
