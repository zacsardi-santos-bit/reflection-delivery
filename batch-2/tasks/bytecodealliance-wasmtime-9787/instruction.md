Implement support for integer-to-float conversions and correct bit reinterpretation operations in the Winch compiler backend for AArch64. Ensure that all combinations of signed and unsigned integer conversions to floating-point numbers are correctly handled, and fix the reinterpretation operations to preserve bit patterns.

*   Implement signed integer-to-float conversions:
    *   Support 32-bit and 64-bit integers converting to 32-bit and 64-bit floats.
    *   Use the AArch64 `scvtf` instruction with appropriate register sizes for each combination.
*   Implement unsigned integer-to-float conversions:
    *   Support 32-bit and 64-bit integers converting to 32-bit and 64-bit floats.
    *   Use the AArch64 `ucvtf` instruction with appropriate register sizes for each combination.
*   Ensure integer-to-float conversions work correctly for:
    *   Compile-time constants
    *   Local variables
    *   Function parameters
    *   Values spilled to the stack
*   Correct the reinterpret-integer-as-float operation:
    *   Use a bit-preserving `fmov` instruction.
    *   Emit `fmov s<dst>, w<src>` for 32-bit integer to float.
    *   Emit `fmov d<dst>, x<src>` for 64-bit integer to float.
*   Correct the reinterpret-float-as-integer operation:
    *   Use a bit-preserving vector move instruction.
    *   Emit `mov w<dst>, v<src>.s[0]` for 32-bit float to integer.
    *   Emit `mov x<dst>, v<src>.d[0]` for 64-bit float to integer.
*   Ensure reinterpret operations work correctly for:
    *   Compile-time constants
    *   Local variables
    *   Function parameters
    *   Spilled values

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.