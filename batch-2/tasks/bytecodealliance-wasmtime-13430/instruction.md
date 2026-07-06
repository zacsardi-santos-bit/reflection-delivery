I've found a bug where the WASI runtime doesn't properly enforce read-only file permissions when a guest WebAssembly module tries to truncate a file.

*   Must add new test program binaries for the read-only truncation scenario: one targeting the WASI p1 interface and at least one targeting the WASI p2 (component) interface. These programs must attempt to truncate a file inside a preopened directory and handle rejection gracefully without trapping.

*   Must register these new test programs in the test artifacts build system so that constants P1_FILE_TRUNCATION_READONLY, P1_FILE_TRUNCATION_READONLY_COMPONENT, and P2_FILE_TRUNCATION_READONLY_COMPONENT are exported from the test_programs_artifacts crate and are accessible via glob import.

*   The WASI runtime must reject file truncation operations when the file was preopened with read-only file permissions (FilePerms::READ only, without write). When a guest runs with a preopened directory mounted with DirPerms::READ | DirPerms::MUTATE and FilePerms::READ, any attempt to truncate a file in that directory must be denied by the runtime.

*   After a guest program that attempts a read-only file truncation finishes executing, the file's contents on the host must remain exactly the bytes b"truncation test file\n" — verifying that no truncation took place.

*   The guest program must exit successfully (not trap or panic) even when the truncation is rejected by the runtime, so that the host-side test can complete and verify the file is unchanged. The guest program must actively assert that the truncation attempt returns an error (the runtime must not silently succeed without truncating) and must also re-verify from within the guest that the file still contains its original contents after the failed attempt.

*   The wasi-common crate's async and sync test files must each include a placeholder p1_file_truncation_readonly test function (to satisfy the foreach_p1!(assert_test_exists) macro that requires every p1 artifact constant to have a corresponding test function in each test suite).

*   The foreach_p1! and foreach_p2! artifact enumeration macros must include the new constants, ensuring that existing test infrastructure correctly discovers and validates the new test programs.


*   Interface details: Type: Constant
Name: P1_FILE_TRUNCATION_READONLY
Location: crates/test-programs/ build artifacts (exported from test_programs_artifacts crate)
Description: Path constant pointing to a compiled WASI p1 module binary that attempts to truncate a file in a preopened read-only directory and handles failure gracefully. Must be exported from test_programs_artifacts and included in the foreach_p1! macro enumeration.

Type: Constant
Name: P1_FILE_TRUNCATION_READONLY_COMPONENT
Location: crates/test-programs/ build artifacts (exported from test_programs_artifacts crate)
Description: Path constant pointing to a compiled WASI component binary (p1-compatible) that attempts to truncate a file in a preopened read-only directory. Must be exported from test_programs_artifacts and included in the foreach_p2! macro enumeration.

Type: Constant
Name: P2_FILE_TRUNCATION_READONLY_COMPONENT
Location: crates/test-programs/ build artifacts (exported from test_programs_artifacts crate)
Description: Path constant pointing to a compiled WASI component binary (p2-native) that attempts to truncate a file in a preopened read-only directory. Must be exported from test_programs_artifacts and included in the foreach_p2! macro enumeration.

Type: Binary (WASI test program)
Name: p1_file_truncation_readonly (source file)
Location: crates/test-programs/src/bin/ (compiled to wasm32-wasip1 target)
Description: WASI p1 test program that opens a file named "test.txt" inside the preopened directory named "readonly", attempts to truncate it, handles the resulting error without panicking or trapping, and exits cleanly with a success code.

Type: Binary (WASI test program)
Name: p2_file_truncation_readonly (source file)
Location: crates/test-programs/src/bin/ (compiled as a WASI component)
Description: WASI p2 (component model) test program that opens a file named "test.txt" inside the preopened directory named "readonly", attempts to truncate it, handles the resulting error without panicking or trapping, and exits cleanly.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.