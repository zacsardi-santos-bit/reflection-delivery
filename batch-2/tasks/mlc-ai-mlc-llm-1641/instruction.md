Restructure the model compilation integration test to defer the construction of hardware target objects until test execution. Extend the test to include additional deployment platforms: Apple Metal, Android, and iOS.

*   Update `DEVICE2TARGET` in `tests/python/integration/test_model_compile.py`:
    *   Store target specifications as plain Python dicts or strings.
    *   Ensure 'cuda', 'rocm', and 'vulkan' entries are plain dicts with hardware specification fields.
    *   Keep 'wasm' as the plain string 'webgpu'.
    *   Add new entries: 'metal' as 'metal', 'android' as 'android', and 'ios' as 'iphone'.

*   Update `DEVICE2SUFFIX` in `tests/python/integration/test_model_compile.py`:
    *   Include new entries for the platforms:
        *   'metal' mapped to 'dylib'
        *   'android' mapped to 'tar'
        *   'ios' mapped to 'tar'

*   Modify `test_model_compile` function in `tests/python/integration/test_model_compile.py`:
    *   Use lazy target construction by retrieving the raw value from `DEVICE2TARGET`.
    *   If the target value is not a string, wrap it in `tvm.target.Target` and convert it to a string.
    *   If the target value is a string, use it directly.
    *   Retrieve the compiled artifact suffix from `DEVICE2SUFFIX` using the same device key.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.