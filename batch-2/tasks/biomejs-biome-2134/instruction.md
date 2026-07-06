Implement enhancements to the JavaScript/TypeScript formatter to correctly handle specific nested patterns. Ensure proper indentation and comment placement for deeply nested conditional expressions and complex TypeScript union types.

*   Format multi-level nested conditional (ternary) expressions:
    *   With tab indentation:
        *   Indent outer `?` and `:` tokens by 1 tab relative to the test expression.
        *   Indent outer branch content by 2 tabs.
        *   Indent inner `?` and `:` tokens by 2 tabs.
        *   Indent inner branch content by 3 tabs.
    *   With 4-space indentation:
        *   Place outer `?` and `:` tokens at 4 spaces.
        *   Place outer branch content at 6 spaces.
        *   Place inner `?` and `:` tokens at 8 spaces.
        *   Place inner branch content at 10 spaces.
    *   Preserve inline comments on `?` and `:` operator tokens on the same line as their respective operator.

*   Format TypeScript union type declarations with nested object types:
    *   With tab indentation:
        *   Indent outer union members by 1 tab.
        *   Indent object member content by 3 tabs.
        *   Indent nested union at 3 tabs.
        *   Indent nested object content at 5 tabs.
    *   With 4-space indentation:
        *   Maintain correct relative indentation for outer and inner union members and their object type contents.

*   Ensure the formatter test infrastructure can discover and run new spec test directories under `crates/biome_js_formatter/tests/specs/`, comparing actual formatter output against expected output in corresponding `.snap` snapshot files.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.