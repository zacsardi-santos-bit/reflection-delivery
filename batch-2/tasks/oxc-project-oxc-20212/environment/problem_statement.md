## Description

The JavaScript minifier is too conservative when analyzing side effects for well-known built-in globals and their properties. Accessing a standard global object (like the math utility, array constructor, data serialization helper, etc.) or reading a well-known property from one of these globals should be recognized as completely side-effect-free. Currently it sometimes marks these as potentially having side effects, which prevents dead code elimination and variable inlining from working as well as they could.

Additionally, certain minification patterns that should be further simplified are being left partially optimized. When a loop that copies function arguments is rewritten to use spread syntax, the resulting intermediate variable should also be eliminated if it is only used once. And when a condition-based branch with a repeated function call can be expressed as a single call with a logical OR, that reduction should happen.

## Expected Behavior

- Accessing any well-known built-in global identifier must be treated as side-effect-free
- Reading a recognized property of a built-in global must be treated as side-effect-free; unknown properties on those globals must still be flagged as potential side effects
- Three-level access chains on the base prototype object must be side-effect-free for the well-known prototype methods; other three-level chains should not receive this special treatment
- Well-known browser and environment-specific globals should be treated as side-effect-free only when the user has explicitly declared them as known globals
- Unknown user globals must still be treated as having side effects even when declared
- Variable inlining should eliminate intermediate variables introduced by the arguments-copy-loop optimization when those variables have only one use
- The built-in unique-symbol constructor must be treated as a pure call that can be inlined
- This behavior should align with how popular JavaScript bundlers handle known global references

## Why This Matters

Without accurate side-effect classification for built-in globals, the minifier cannot safely remove code that reads but never uses these values, and cannot inline variables that only hold the results of accessing them. This leads to unnecessarily large output and mismatches with what users expect from modern JavaScript tooling.
