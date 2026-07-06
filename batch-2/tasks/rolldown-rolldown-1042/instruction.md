Reorganize the test configuration file loading logic for a Rust bundler project by moving it into a dedicated submodule. Ensure the new module provides a standalone function for reading and parsing configuration files, maintaining the same behavior as the existing method.

*   Create a public module named `test_config` within the `rolldown_testing` crate.
    *   Ensure it is accessible at the path `rolldown_testing::test_config`.
*   Within the `test_config` module:
    *   Publicly re-export the `TestConfig` type so it is accessible as `rolldown_testing::test_config::TestConfig`.
    *   Implement a public free function `read_test_config` with the signature `read_test_config(config_path: &std::path::Path) -> TestConfig`.
        *   The function must read and parse a JSON test configuration file from the given path.
        *   Return a `TestConfig` instance with behavior equivalent to the previous method on the `TestConfig` type.

*   Update the test infrastructure to use the new module path and function.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.