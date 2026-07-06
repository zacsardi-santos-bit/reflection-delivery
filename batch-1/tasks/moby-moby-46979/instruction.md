Update all import references in the moby codebase to use the new canonical module path for the mergo library. Ensure that the module manifest, checksum file, and vendored source tree reflect this change. Verify that all existing tests compile and pass after the update.

*   Replace all occurrences of the old import path 'github.com/imdario/mergo' with 'dario.cat/mergo' in:
    *   Production source files, such as `daemon/config/config.go`.
    *   Test files, including `daemon/config/config_test.go` and `daemon/runtime_unix_test.go`.

*   Update the module manifest file:
    *   Modify `vendor.mod` to declare 'dario.cat/mergo v1.0.0' as a dependency.
    *   Remove any entries for 'github.com/imdario/mergo'.

*   Adjust the vendor directory:
    *   Ensure the mergo library is located under `vendor/dario.cat/mergo/`.
    *   Remove the directory `vendor/github.com/imdario/mergo/`.

*   Revise the vendor module index file:
    *   Update `vendor/modules.txt` to reference 'dario.cat/mergo v1.0.0'.

*   Modify the checksum file:
    *   Ensure `vendor.sum` contains correct hash entries for 'dario.cat/mergo v1.0.0'.
    *   Remove any entries for 'github.com/imdario/mergo'.

*   Confirm that all existing tests in the `daemon` and `daemon/config` packages compile and pass without any changes in behavior.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.