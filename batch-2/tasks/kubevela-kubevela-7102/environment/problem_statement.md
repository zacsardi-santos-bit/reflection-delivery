## Description

The CUE code generator in the component definition toolkit emits invalid CUE when optional collection parameters (lists and maps) are referenced in conditions and template output. Specifically, it uses dot-notation to access these fields (e.g. directly accessing the field by name), which causes a strict-mode evaluation failure because CUE rejects direct access to fields that may be absent. Additionally, checking whether an optional collection is "absent or empty" is expressed as a single condition, but CUE's boolean OR operator cannot safely handle this case — it requires two separate conditional blocks.

## Expected Behavior

- All conditions that reference collection parameters (checking length, containment, map key existence) should use bracket-existence guards instead of dot-notation, matching the idiom used in other KubeVela built-in component definitions.
- The "empty or absent" check for optional collections should expand into two separate if blocks at render time: one for the field being absent, and one for the field being present but empty.
- Conditional template output targeting bracket-notation path keys should correctly wrap the key assignment inside if blocks when a condition is present, and emit unconditionally when no condition is set.
- The convenience string-keyed map parameter type should expose the same predicate methods (key presence check, emptiness, non-emptiness, length comparisons) as the generic map type, so callers don't need to switch types just to use these predicates.
- When an "or-of-variants" parameter has a default value set, the generated schema should drop the optional marker from the discriminator field, since the default makes the value concrete.
- The auto-import scanner should detect when array constraints like minimum/maximum item counts are in use and automatically include the required CUE standard library import.

## Why This Matters

Components that use optional lists or maps in their parameter definitions generate broken CUE that fails at runtime in strict mode. Users working with these components encounter unexpected evaluation errors that have nothing to do with their actual configuration. This fix ensures the generated CUE is correct for all optional collection parameter types without requiring users to manually work around the limitation.
