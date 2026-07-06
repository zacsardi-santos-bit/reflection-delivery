## Description

The summarization middleware stores an internal representation of its trigger conditions after initialization. Currently, there is only one such internal attribute, and it is used as both the primary driver of summarization logic and the representation inspected by downstream code. The problem is that this single representation cannot correctly handle compound triggers — cases where multiple conditions must all be met simultaneously (AND semantics). Compound multi-condition triggers end up being misrepresented or silently dropped, because the representation assumes all conditions are independent single-metric thresholds.

## Expected Behavior

- The middleware should maintain a **canonical internal representation** of triggers that accurately captures all trigger variants, including compound AND clauses (dicts with multiple conditions).
- The middleware should also maintain a **legacy compatibility representation** that contains only the simple single-condition triggers expressible as key-value pairs, for backward compatibility with code that previously inspected that internal state.
- Compound AND triggers (triggers requiring multiple conditions to be simultaneously satisfied) should be present in the canonical representation but absent from the legacy compatibility view.
- Single-condition triggers (both tuple-form and single-key dict-form) should appear in both representations, normalized appropriately.

## Why This Matters

Developers extending or inspecting the middleware's trigger state currently get an inaccurate picture when compound triggers are in use. This can lead to incorrect behavior when the middleware's internal trigger state is read by tools, subclasses, or debugging utilities. Having a clearly named canonical representation and a clearly named legacy view removes this ambiguity.
