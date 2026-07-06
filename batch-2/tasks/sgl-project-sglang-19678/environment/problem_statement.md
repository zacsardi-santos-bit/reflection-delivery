## Description

Two improvements are needed for the tensor dimension annotation system used in the distributed-model debugging and comparison tool.

**1. Syntax clarification for dimension modifiers**

The current syntax for annotating how a tensor dimension is distributed across parallel processes uses parentheses (e.g. a dimension sharded across tensor-parallel ranks is written with a parenthesized label). This conflicts visually with other uses of parentheses in the format and leads to ambiguity. The annotation delimiter should be changed to square brackets throughout — in all parsing functions, in all places that construct or return dimension strings, and in any output or internal representation that serializes these annotations.

**2. Support for fused (combined) dimensions**

Currently there is no way to express that multiple logically distinct dimensions have been physically merged into a single contiguous block in memory. This matters because different frameworks or model implementations often differ in whether dimensions like attention-head count and per-head feature size are stored separately or combined into a single flat axis.

A new "fused dimension" syntax should be introduced: a parenthesized group of sub-dimension names joined by asterisks represents a single physical axis that is the product of those logical sub-dimensions. Fused dimensions should support the same parallel-axis modifiers as regular dimensions (using the square-bracket syntax from point 1).

## Expected Behavior

- All existing dimension strings should be updated to use square brackets for modifiers.
- A fused dimension should be parseable alongside regular dimensions in a full dimension specification.
- Each dimension descriptor should expose whether it is fused, what its constituent sub-dimension names are, and a canonical name that joins those sub-names with a triple-underscore separator.
- When comparing two tensors that represent the same data but differ in whether those sub-dimensions are stored fused or separately, the comparison tool should automatically generate a plan to reshape (flatten) the tensor with separate dimensions so it matches the fused one before doing numerical comparison.
- Incompatible fused groupings (e.g. overlapping sub-dimensions across the two sides) should be detected and reported with an appropriate warning.

## Why This Matters

Without fused dimension support, the comparison tool fails to align tensors from implementations that store the same data with different memory layouts, producing false mismatches. The syntax clarification also removes an ongoing source of parsing confusion for users writing dimension strings manually.
