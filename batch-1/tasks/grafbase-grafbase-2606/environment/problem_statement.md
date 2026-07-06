## Description

When defining federated schema extensions with typed directive arguments, the engine does not validate argument values at schema construction time. This means errors like providing the wrong value type for a directive argument, omitting required arguments, using undefined types in the extension schema, or passing unknown arguments all go undetected until runtime — or silently produce incorrect behavior.

## Expected Behavior

- Directive argument values should be checked against their declared types when the schema is built.
- If an argument references an undefined type or a type that cannot be used as an input (e.g., a plain object type instead of an input object), schema construction should fail with a clear error.
- Providing values of the wrong kind — wrong scalar type, wrong enum format, wrong object shape — should fail with a descriptive error indicating the directive name, the location (field or schema), and the precise path to the problematic value.
- Providing extra unknown arguments to a directive or omitting required arguments should produce clear errors.
- Standard type coercions (e.g., promoting a single value to a list, converting a whole-number float to an integer) should be handled automatically.
- Default values defined on directive arguments or input object fields should be applied when values are omitted.
- Distinguishing between explicitly providing a null value versus not providing an argument at all should be supported.

## Why This Matters

Misconfigured extension directives are currently hard to debug because there are no early validation errors. Catching these mistakes at build time gives developers actionable feedback before the schema goes live.
