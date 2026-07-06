## Description

The expression evaluation subsystem for filtering rows in Dolt is still using an old, legacy internal storage format to represent values and rows during comparison. The comparison operators carry a reference to a storage backend object that is no longer needed, and row data must be provided as a proprietary map-based structure rather than as ordinary SQL rows. This creates unnecessary coupling to the legacy storage layer and makes the code harder to maintain and extend.

The goal is to refactor the expression evaluator and the comparison operator types so they work directly with the SQL engine's native row representation. Comparison operators should be stateless (no stored backend references), and all predicate functions should accept standard SQL context and row types rather than the legacy value types.

## Expected Behavior

- Comparison operator types should carry no state — they should be instantiable as zero values with no constructor arguments
- The method for handling null comparisons should accept a simple boolean indicating whether the other value is also null, rather than a full legacy value object
- A new method for interpreting integer comparison results (negative/zero/positive) should be available on each operator type
- Functions that build row-filtering predicates should accept a native SQL schema and return predicate functions that operate on native SQL rows
- Logical combiner functions (AND, OR) should work with the updated predicate function signatures

## Why This Matters

Keeping the expression evaluator tied to the old storage format creates a maintenance burden and makes it harder to evolve the SQL layer independently. Removing this coupling simplifies the code and ensures the filtering logic is consistent with how the rest of the SQL engine handles rows and schemas.
