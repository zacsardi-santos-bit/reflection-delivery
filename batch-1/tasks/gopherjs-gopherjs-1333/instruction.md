Implement the necessary changes to ensure GopherJS compatibility with the updated Go standard library. Move and update native overlay files for elliptic curve cryptography packages and introduce a mechanism to cap test iterations for property-based tests.

*   Implement the `GopherJSInternalMaxCountCap` function in a new file:
    *   File path: `compiler/natives/src/testing/quick/quick.go`
    *   Build constraint: `//go:build js`
    *   Function signature: `GopherJSInternalMaxCountCap(newCap int) (restore func())`
    *   Functionality: Set a global upper bound on property-based test iterations and return a cleanup function to restore the previous cap.

*   Ensure the `crypto/internal/edwards25519/field` package compiles under GopherJS:
    *   Place native overlay files in `compiler/natives/src/crypto/internal/edwards25519/field/`.

*   Update the location of native overlay test files:
    *   Move test files to `compiler/natives/src/crypto/internal/edwards25519/` to match Go 1.20's standard library layout.

*   Remove outdated native overlay files:
    *   Delete the following files as they correspond to obsolete package paths:
        *   `compiler/natives/src/crypto/ed25519/internal/edwards25519/field/fe_test.go`
        *   `compiler/natives/src/crypto/ed25519/internal/edwards25519/scalar_test.go`
        *   `compiler/natives/src/crypto/ed25519/ed25519vectors_test.go`

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.