Implement types and functions for elliptic curve cryptography in the Sway standard library's crypto module. Create a two-dimensional curve point type and a scalar value type, along with functions for elliptic curve operations such as point addition, scalar multiplication, and pairing checks.

*   Implement the `Point2D` type in `sway-lib-std/src/crypto/point2d.sw`.
    *   Ensure it is importable via `use std::crypto::point2d::*`.
    *   Provide a `new()` method that returns a point with zero-length x and y byte sequences.
    *   Implement `zero()` and `min()` methods returning a point with x and y as 32-byte zero values.
    *   Implement `is_zero()` to return true if both coordinates are zero.
    *   Implement `x()` and `y()` methods returning 32-byte sequences with a capacity of 32.
    *   Support construction via `From` for tuples and arrays of `b256`, `u256`, and 64-element `u8`.
    *   Implement `TryFrom` for conversion back to tuples and arrays of `b256` and `u256`.
    *   Implement equality comparison (`PartialEq`) for `Point2D`.

*   Implement the `Scalar` type in `sway-lib-std/src/crypto/scalar.sw`.
    *   Ensure it is importable via `use std::crypto::scalar::*`.
    *   Provide a `new()` method that returns a scalar with zero-length byte sequence.
    *   Implement `zero()` and `min()` methods returning a scalar with a 32-byte zero value.
    *   Implement `is_zero()` to return true if the scalar represents zero.
    *   Implement `bytes()` to return a 32-byte sequence with a capacity of 32.
    *   Support construction via `From` for `b256`, `u256`, and 32-element `u8` arrays.
    *   Implement `TryFrom` for conversion back to `u256` and `b256`.

*   Implement elliptic curve functions in `sway-lib-std/src/crypto/alt_bn128.sw`.
    *   Ensure `alt_bn128_add`, `alt_bn128_mul`, and `alt_bn128_pairing_check` are importable via `use std::crypto::alt_bn128::*`.
    *   Implement `alt_bn128_add(p1: Point2D, p2: Point2D) -> Point2D` for point addition on the alt_bn128 curve, reverting on invalid points.
    *   Implement `alt_bn128_mul(p: Point2D, s: Scalar) -> Point2D` for scalar multiplication, reverting on invalid points.
    *   Implement `alt_bn128_pairing_check(points: Vec<(Point2D, [Point2D; 2])>) -> bool` for bilinear pairing checks, reverting on invalid points.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.