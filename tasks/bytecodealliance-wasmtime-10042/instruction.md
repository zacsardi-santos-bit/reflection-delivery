Implement support for SIMD lane extraction operations in the Winch compiler's x86-64 backend using AVX instructions. Ensure that the compiler can handle all specified element types and correctly generate machine code for SIMD lane extraction operations.

*   Implement all eight SIMD lane extraction operations in the x86-64 backend:
    *   i8x16 (signed and unsigned)
    *   i16x8 (signed and unsigned)
    *   i32x4
    *   i64x2
    *   f32x4
    *   f64x2

*   Use the appropriate AVX instructions for integer extractions:
    *   i8x16: Use `vpextrb`
    *   i16x8: Use `vpextrw`
    *   i32x4: Use `vpextrd`
    *   i64x2: Use `vpextrq`
    *   Place results in a general-purpose register.

*   Handle signed integer extractions:
    *   i8x16.extract_lane_s: Sign-extend extracted byte to 32 bits.
    *   i16x8.extract_lane_s: Sign-extend extracted 16-bit value to 32 bits.

*   Handle unsigned integer extractions without sign extension:
    *   i8x16.extract_lane_u
    *   i16x8.extract_lane_u
    *   i32x4.extract_lane
    *   i64x2.extract_lane

*   For floating-point extractions:
    *   f32x4 and f64x2 lane 0: No additional instruction needed.
    *   f32x4 lane 1: Use `vpshufd` with immediate value 1.
    *   f64x2 lane 1: Use `vpshufd` with immediate value 0xee.

*   Ensure AVX support is required:
    *   Bail with an appropriate error on targets without AVX.

*   Place new test files in the specified paths:
    *   `tests/disas/winch/x64/f32x4_extract_lane/first_lane_avx.wat`
    *   `tests/disas/winch/x64/f32x4_extract_lane/second_lane_avx.wat`
    *   `tests/disas/winch/x64/f64x2_extract_lane/first_lane_avx.wat`
    *   `tests/disas/winch/x64/f64x2_extract_lane/second_lane_avx.wat`
    *   `tests/disas/winch/x64/i16x8_extract_lane_s/const_avx.wat`
    *   `tests/disas/winch/x64/i16x8_extract_lane_u/const.wat`
    *   `tests/disas/winch/x64/i32x4_extract_lane/const_avx.wat`
    *   `tests/disas/winch/x64/i64x2_extract_lane/const.wat`
    *   `tests/disas/winch/x64/i8x16_extract_lane_s/const_avx.wat`
    *   `tests/disas/winch/x64/i8x16_extract_lane_u/const_avx.wat`

*   Ensure each test file declares target x86_64, test winch, and includes flags like `-Ccranelift-has-avx`. Match expected assembly comments to the exact emitted instructions.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.