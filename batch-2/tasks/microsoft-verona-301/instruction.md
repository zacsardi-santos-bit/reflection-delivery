Update the build configuration to use the allocator library's specific flag for pass-through mode instead of the generic system allocator name. Ensure all related code references the new flag and adjust test parameters as specified.

*   Define the preprocessor macro `SNMALLOC_PASS_THROUGH` in the build system:
    *   Replace the previously used `USE_MALLOC` macro with `SNMALLOC_PASS_THROUGH` in `src/rt/CMakeLists.txt`.
    *   Use `-DSNMALLOC_PASS_THROUGH` as a compile definition via `target_compile_definitions`.

*   Modify memory pool tests:
    *   Skip memory pool acquisition and release tests when `SNMALLOC_PASS_THROUGH` is defined, as pool-based allocation is inactive.
    *   Ensure that when `SNMALLOC_PASS_THROUGH` is not defined, tests for `current_alloc_pool()` function correctly:
        *   Acquiring two separate pool objects should yield distinct objects.
        *   Releasing and re-acquiring should return the previously released object.

*   Adjust the concurrent ownership weak reference test:
    *   Change the tree depth parameter from 10 to 9 to align with updated behavior in the underlying data structure.
    *   Ensure the runtime schedules and handles weak reference behaviors correctly on this modified tree structure.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.