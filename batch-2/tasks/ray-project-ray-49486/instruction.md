Implement a generic result wrapper class template `StatusOr<T>` in the Ray C++ codebase to encapsulate either a valid value or an error status. Ensure that this wrapper supports various operations like construction, comparison, and chaining, and provide test utilities for ease of testing.

*   Implement `StatusOr<T>` class template in `src/ray/common/status_or.h` within the `ray` namespace.
    *   Ensure it is implicitly constructible from a `Status` object and a `T` value.
    *   Support move construction from an rvalue `T`.
    *   Include a default constructor, and support copy and move construction/assignment.
    *   Allow implicit conversion from `StatusOr<Derived>` to `StatusOr<Base>` using a template constructor.
    *   Implement `ok()` and `operator bool()` to check if the instance holds a value.
    *   Provide `status()` to return the contained `Status` and `code()` to return the `StatusCode`.
    *   Implement `value()` to return a reference to the contained `T`, with a fatal check if not ok.
    *   Support `operator*()` for dereferencing and `operator->()` for member access.
    *   Provide `value_or(default_val)` to return the value if ok, otherwise the provided default.
    *   Implement `value_or_default()` to return the value if ok, otherwise a default-constructed `T{}`.
    *   Define `operator==` and `operator!=` for equality comparison based on value or error status.
    *   Implement `and_then(f)` to apply a function to the value if present, otherwise return the error.
    *   Implement `or_else(f)` to apply a function to recover from an error, otherwise return the value.
    *   Include a `swap` method for swapping contents with another `StatusOr`.

*   Define free functions in the `ray` namespace within the same file:
    *   `template<typename T> bool operator==(const StatusOr<T> &lhs, const StatusOr<T> &rhs)`
    *   `template<typename T> bool operator!=(const StatusOr<T> &lhs, const StatusOr<T> &rhs)`
    *   `template<typename T> void swap(StatusOr<T> &lhs, StatusOr<T> &rhs)`

*   Create test utility macros in `src/ray/common/test/testing.h`:
    *   Define `RAY_EXPECT_OK(s)` to expand to `EXPECT_TRUE((s).ok())`.
    *   Define `RAY_ASSERT_OK(s)` to expand to `ASSERT_TRUE((s).ok())`.

*   Update Bazel build files:
    *   In `src/ray/common/BUILD`, add a `ray_cc_library` named "status_or" with `hdrs = ["status_or.h"]` and a dependency on ":status".
    *   In `src/ray/common/test/BUILD`, add a `ray_cc_library` named "testing" with `hdrs = ["testing.h"]` and `testonly = True`.
    *   Add a `ray_cc_test` named "status_or_test" depending on ":testing", "//src/ray/common:status_or", and "@com_google_googletest//:gtest_main".

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.