## Description

Variant inference does not work correctly for the language's built-in types. The compiler has a feature where, if a value is known to always hold a specific constructor of a type, it can narrow the required pattern matches accordingly. For example, if a value is always constructed as the successful variant of a result type, the compiler should know it can only ever be that variant and allow pattern matching without requiring branches for the other constructor.

However, this variant inference only works for user-defined types. For the built-in types provided by the prelude (booleans, results, etc.), the compiler always treats values as potentially being any variant — even when the constructor used is clearly a constant literal. This means developers are forced to write unnecessary catch-all patterns or get incorrect exhaustiveness errors when working with constant built-in values.

## Expected Behavior

- When a value is constructed using a specific built-in constructor (e.g., always the successful variant of a result), the compiler should infer the variant and allow exhaustive pattern matching without requiring branches for other variants.
- When pattern matching on a literal boolean true value, only a branch for the true case should be required — no wildcard or branch for the false case is needed, since the value can only ever be true.
- The "add missing patterns" language server code action should work correctly when the subject is a typed function parameter, not just when it is a let-bound literal value.

## Why This Matters

Developers lose the benefit of the compiler's narrowing/variant inference when working with built-in types, even in cases where the value is clearly constant. This leads to unnecessary boilerplate and potentially misleading exhaustiveness errors.
