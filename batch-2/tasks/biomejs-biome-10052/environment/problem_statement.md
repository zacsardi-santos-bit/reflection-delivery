## Description

The lint rule that detects misleadingly wide return type annotations is currently incomplete: it does not flag one of the most common real-world patterns, where a function's return type annotation includes extra union variants (like "or null", "or number") that the function never actually returns.

For example, a function declared to return a string-or-null that always returns a string should be flagged, because callers are misled into thinking they need to handle the null case. The rule should also suggest the correct, narrower type so developers know exactly what to change.

## Expected Behavior

- Functions with union return types (such as unions with a nullable variant, two-type unions, or three-way unions) should be flagged when the body never returns some of those variants.
- The diagnostic should include a concrete suggestion of the narrower type when one can be determined (e.g. a message recommending the specific narrowed type to use instead).
- This should work across all function forms: regular functions, async functions with wrapped return types, arrow functions, class methods, class getters, and object literal methods.
- Type aliases that resolve to union types should be handled the same as inline unions.
- Functions with branches where one path throws should correctly ignore the throwing path when computing what types are actually returned.
- A specific case where a precise type variant could be suggested instead of a generic note should now produce the specific suggestion.

## What Should NOT Be Flagged

- Unions where all variants are genuinely returned across different branches.
- Unions containing special top types that absorb all other members.
- Unions where literal types are entirely subsumed by their corresponding primitive base type (e.g. a union of specific literal values and the broader primitive type that encompasses them, which effectively collapses to that primitive).

## Why This Matters

Without this coverage, developers routinely write overly broad return type annotations that mislead callers and prevent them from relying on narrower types. The lint rule's value is significantly reduced when it misses the most common form of this problem.
