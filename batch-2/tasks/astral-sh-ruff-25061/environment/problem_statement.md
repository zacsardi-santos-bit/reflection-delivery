## Description

The lint rule that detects redundant duplicate type-checking calls — where the same object is checked against different types across two separate "or" branches — has a bug in its auto-fix suggestion. When the expression being type-checked is a complex interpolated string containing embedded functions or special escape sequences in the format specifier, the auto-fix corrupts that expression. Instead of preserving the original source text, the fix re-renders the expression through the internal representation, causing escape sequences to be changed and embedded anonymous functions to be reformatted or broken entirely.

Additionally, the rule does not correctly handle cases where the type argument is expressed as a tuple with starred unpacking, or where one of the type arguments is an empty tuple. These patterns either produce incorrect fix suggestions or cause unexpected behavior.

## Expected Behavior

- When an interpolated string target contains escape sequences in its format spec, the auto-fix must preserve the original source text of that string exactly.
- When an interpolated string target contains a lambda expression in its interpolation, the auto-fix must preserve the original source text exactly.
- When a type argument is a tuple containing starred unpacking (e.g., expanding a variable holding multiple types), the rule must flatten the elements correctly into the merged result.
- When one of the duplicate type-checking calls uses an empty tuple as its type argument, the rule must handle this gracefully and produce a valid fix.
- Parenthesized type-checking expressions (either the outer expression or individual operands) must be correctly detected and fixed.

## Why This Matters

Users who rely on the auto-fix to simplify their type checks may end up with syntactically broken or semantically different code when the target expression is a complex interpolated string or when starred unpacking is involved. This undermines trust in the auto-fix and can introduce subtle bugs.
