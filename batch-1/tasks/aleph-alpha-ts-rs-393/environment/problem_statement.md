## Description

The TypeScript type generation library currently has two related gaps that produce incorrect or incomplete TypeScript output.

**Gap 1 — Tuple structs ignore optional/nullable settings**

The library supports annotations on named struct fields that control whether a field appears as optional or nullable in the generated TypeScript. However, the same annotations have no effect when applied to positional fields in tuple-style structs. Struct-level settings that apply the optional/nullable behavior to all eligible fields also do not work for tuple structs. This means developers who use tuple structs cannot produce TypeScript tuple types with optional elements — the annotations are silently ignored.

**Gap 2 — Serialization-skip attributes are not reflected in TypeScript output**

When the serialization compatibility feature is enabled, the library already respects some serialization annotations. However, it does not recognize the pattern where a field is configured to be skipped during serialization (either always or conditionally) while also having a default value. In serialization terms, such a field may be absent from serialized output, which means the TypeScript type should reflect that the field is optional. Currently this inference is not performed.

## Expected Behavior

- Tuple struct elements with per-element optional annotations should produce the correct optional or optional-nullable encoding in TypeScript tuple types.
- Struct-level optional/nullable settings should apply to tuple structs in the same way they apply to named structs, respecting explicit per-element overrides.
- When the serialization compatibility feature is active, fields marked to skip serialization combined with a default value should automatically become optional in the generated TypeScript output; fields with an optional wrapper type should also be nullable.
- Explicit per-field overrides should take precedence over both struct-level settings and serialization-inferred behavior.
- A struct-level setting that disables automatic optional inference should suppress serialization-based inference for all fields unless a field explicitly re-enables it.

## Why This Matters

Without these fixes, developers must manually adjust generated TypeScript types or avoid using tuple structs and certain serialization patterns. The generated types are inaccurate: fields that may be absent during serialization appear as required in TypeScript, which can cause runtime errors on the TypeScript side when a value that was expected to be present is actually missing.
