## Description

When a workflow task produces a dataclass output that contains fields of various numeric types — including both signed and unsigned integers of different sizes, floating-point values, booleans, and strings — and a downstream task accesses one of those fields through a promise, the field resolver currently wraps the extracted value back into a binary-encoded blob instead of returning it as a properly typed primitive value.

## Expected Behavior

- When traversing a binary-encoded dataclass output and the resolved leaf field contains a primitive value (any integer width, float, string, or boolean), the result should be a properly typed primitive literal rather than a raw binary blob.
- All commonly used integer widths — both signed and unsigned — should be supported and converted to the appropriate typed integer literal.
- Unsigned integer values that are too large to be safely represented as a signed 64-bit integer should produce a clear, descriptive error rather than silently overflowing.

## Why This Matters

Downstream tasks that receive a typed primitive value can work with it directly. Receiving an opaque binary blob instead forces consumers to know the encoding format and decode it manually, which breaks type-safe data flow in the workflow engine. Supporting all common numeric widths also aligns with what Python dataclasses naturally produce when they include fields with specific integer types.
