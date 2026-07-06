Implement a feature in the biome JavaScript formatter to correctly handle empty lines within member chain expressions. Ensure that the formatter distinguishes between chains that should be collapsed into a single line and those that qualify for multiline formatting, preserving developer intent regarding empty lines.

*   For member chain expressions that qualify to be formatted on a single line:
    *   Collapse all empty lines between chain members.
    *   Produce a compact single-line result.
    *   Include chains of only static property accesses, chains ending with a method call that does not trigger multiline formatting, chains containing a computed member access, and chains with method calls that do not exceed the multiline threshold.

*   For member chain expressions that qualify for multiline formatting:
    *   Preserve empty lines between logical groups as they appear in the original source.
    *   Force multiline formatting if any tail group has an empty line before it in the source.
    *   Ensure each group in the tail with an empty line before it in the source is preceded by an empty line in the formatted output.
    *   Precede groups without an empty line before them with a single line break.
    *   Handle chains where the first group of the tail has an empty line before it, triggering parent group expansion in the formatted output.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.