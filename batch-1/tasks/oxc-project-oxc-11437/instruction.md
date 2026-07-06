Fix the lint rule that checks for precision loss in JavaScript number literals to prevent false positives for numbers that are exactly representable in the 64-bit floating-point format. Ensure that only numbers that genuinely lose precision are flagged.

*   Update the precision-loss lint rule to:
    *   Exclude the number literal 1000000000000000128 from being flagged, as it is exactly representable in IEEE 754 double-precision format.
    *   Evaluate any large integer numeric literal in expression contexts, such as method calls, to determine if it is exactly representable as a 64-bit float before emitting a warning.
    *   Ensure that the rule remains silent for numbers that do not lose precision.

*   Maintain the integrity of the existing lint rule by:
    *   Continuing to pass all existing valid numeric literals, including integers, decimals, scientific notation, and Infinity.
    *   Correctly flagging all existing invalid numeric literals as defined by the original test suite.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.