Update the gRPC build system to support Windows MSVC by modifying the Bazel build helper macros to handle platform-specific tags and detect MSVC builds. Ensure that test targets relying on POSIX polling mechanisms are skipped when building with MSVC.

*   Implement a function `is_msvc` in `bazel/grpc_build_system.bzl`:
    *   Define `def is_msvc()` to return a Bazel select expression evaluating to True when building with MSVC on Windows, and False otherwise.

*   Modify the `grpc_cc_binary` function in `bazel/grpc_build_system.bzl`:
    *   Accept a `tags` parameter in the function signature.
    *   Forward the `tags` parameter to the underlying native binary rule using `"tags": tags`.

*   Modify the `grpc_cc_library` function in `bazel/grpc_build_system.bzl`:
    *   Accept a `tags` parameter in the function signature.
    *   Forward the `tags` parameter to the underlying native library rule using `"tags": tags`.

*   Modify the `grpc_cc_test` function in `bazel/grpc_build_system.bzl`:
    *   Accept a `tags` parameter in the function signature.
    *   Forward the `tags` parameter to the native test rules using `"tags": tags`.
    *   Check `uses_polling and not is_msvc()` to conditionally skip the generation of polling-based test variants when building with MSVC.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.