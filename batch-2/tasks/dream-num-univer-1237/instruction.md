Implement several missing built-in formula functions in a spreadsheet formula engine to handle common operations like checking for blank cells, conditional branching, error handling, boolean logic, text concatenation, and cross-type comparisons. Ensure these functions support both scalar and array inputs with appropriate broadcasting and error handling.

*   Implement the `Isblank` class in `packages/engine-formula/src/functions/information/isblank/index.ts`.
    *   Method signature: `calculate(value: BaseValueObject): BaseValueObject`
    *   Return true for `NullValueObject` inputs and false for `ErrorValueObject`, `BooleanValueObject`, `StringValueObject`, and `NumberValueObject` (including 0).
    *   For `ArrayValueObject`, return an array with element-wise null-check results.

*   Implement the `And` class in `packages/engine-formula/src/functions/logical/and/index.ts`.
    *   Method signature: `calculate(...logicals: BaseValueObject[]): BaseValueObject`
    *   Return true if all logical arguments are true, false if any are false.
    *   Return '#VALUE!' if no logical values are found.
    *   Ignore string values in arrays; propagate error values.

*   Implement the `If` class in `packages/engine-formula/src/functions/logical/if/index.ts`.
    *   Method signature: `calculate(logicTest: BaseValueObject, valueIfTrue: BaseValueObject, valueIfFalse?: BaseValueObject): BaseValueObject`
    *   Return `valueIfTrue` for true `logicTest`, `valueIfFalse` or false for false `logicTest`.
    *   For array inputs, expand results to maximum dimensions, using '#N/A' for out-of-bounds positions.

*   Implement the `Iferror` class in `packages/engine-formula/src/functions/logical/iferror/index.ts`.
    *   Method signature: `calculate(value: BaseValueObject, valueIfError: BaseValueObject): BaseValueObject`
    *   Return `value` unchanged if not an error, or `valueIfError` if it is.
    *   For arrays, apply element-wise substitution and broadcast to maximum dimensions.

*   Implement the `Concatenate` class in `packages/engine-formula/src/functions/text/concatenate/index.ts`.
    *   Method signature: `calculate(...texts: BaseValueObject[]): BaseValueObject`
    *   Join text values or arrays into a single string, handling escaped quotation marks.
    *   Broadcast scalars across arrays, and handle dimension mismatches by using '#N/A' for out-of-bounds.

*   Implement the `valueObjectCompare` function in `packages/engine-formula/src/engine/utils/object-compare.ts`.
    *   Method signature: `valueObjectCompare(currentValue: BaseValueObject, searchValue: BaseValueObject, token: compareToken): BooleanValueObject`
    *   Apply cross-type ordering: numbers < strings < booleans.
    *   Use lexicographic ordering for string comparisons.

*   Update the `ErrorType.NAME` in `packages/engine-formula/src/basics/error-type.ts` to '#NAME?'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.