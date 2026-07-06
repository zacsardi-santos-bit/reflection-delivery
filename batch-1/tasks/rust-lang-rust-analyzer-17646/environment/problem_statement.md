## Description

The parser does not correctly handle all valid forms of dynamic trait object types when processing code targeting older Rust editions (before 2018). In those editions, the keyword used to denote a dynamic trait object is contextual — it is not reserved — so the parser must use lookahead to decide when it should be treated as a type modifier versus an ordinary identifier.

The current lookahead check is too narrow. It only recognizes a small set of tokens that can follow the keyword to trigger dynamic-trait-type parsing. As a result, several valid syntactic forms are missed and either fail to parse or are misinterpreted: types bounded by a lifetime, types prefixed with a question mark, types using a for-binder (higher-ranked trait bounds), and types wrapped in parentheses all go unrecognized. A helper is needed that consolidates this decision into one place and handles all these cases correctly.

## Expected Behavior

- A plain keyword-followed-by-path form → parsed as a dynamic trait type
- A reference to the keyword-qualified form → parsed as a reference containing a dynamic trait type
- The keyword followed by a lifetime bound → parsed as a dynamic trait type with a lifetime bound
- The keyword followed by a question-mark-prefixed bound → parsed as a dynamic trait type with a question-mark bound
- The keyword followed by a for-binder (higher-ranked trait bounds) → parsed as a dynamic trait type with a for-binder
- The keyword followed by a parenthesized bound list → parsed as a dynamic trait type with parenthesized bounds
- The keyword used as a path-segment module name (followed by a path separator) → parsed as an ordinary path type
- The keyword followed by angle-bracket generic arguments → parsed as an ordinary generic path (the keyword acts as a plain identifier)

## Why This Matters

Code written for older Rust editions that uses these syntactic forms should be parsed correctly. Without this fix, valid older-edition code using lifetime bounds, question marks, for-binders, or parenthesized bounds on dynamic trait types will not be recognized as intended, producing incorrect parse trees.
