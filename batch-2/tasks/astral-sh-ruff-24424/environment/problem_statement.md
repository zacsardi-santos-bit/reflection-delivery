## Description

The file permissions lint check has two related gaps:

1. **Missing preview-mode strictness**: The check does not distinguish between stable and preview mode when deciding which permission bits are dangerous. The upstream tool this check is based on flags four distinct dangerous bits (group-write, group-execute, other-write, and other-execute), but the current implementation only flags two of them regardless of whether preview mode is enabled. Group-write and other-execute permissions should become errors in preview mode to match the upstream behavior.

2. **Inability to analyze dynamic permission expressions**: When the mode argument to a permission-setting call is constructed via bitwise operations (for example, combining a runtime variable with a constant), the checker currently gives up entirely and produces no diagnostic. It should instead reason about which bits are statically known to be set or cleared. If the known-set bits include a dangerous permission, the call should be flagged even when part of the expression is unknown. Conversely, if the only way to end up with a dangerous bit would require a specific value of an unknown operand, the call should be left alone.

## Expected Behavior

- Permissions that include group-write or other-execute bits should produce a diagnostic in preview mode but not in stable mode.
- A permission mode formed by combining a variable with a dangerous constant using bitwise OR should be flagged because the constant guarantees those dangerous bits are always present in the result.
- A permission mode formed by masking a variable with a safe constant using bitwise AND should be accepted because the AND operation cannot introduce dangerous bits that are not already present in the constant.
- Oversized integer literals (too large to fit in the platform's native integer type) should be reported as invalid.
- Integer literals with bits set beyond the valid 12-bit Unix permission range should be reported as invalid.
- A bitwise operation between two structurally identical expressions (such as XOR with itself) should be recognized as producing zero.

## Why This Matters

These missing detections cause false negatives: dangerous or malformed permission calls silently pass the check. Developers using preview mode expect stricter checking, and expressions built from bitwise operations are common in code that constructs permissions dynamically, so missing them represents a significant gap in coverage.
