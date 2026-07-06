Implement a new module in the Deno runtime to parse command-line flags into a structured format. Create a new source file to define the necessary data structures and functions to handle recognized flags, separate them from script arguments, and preprocess arguments for the underlying engine.

Requirements:

*   Create a new file `src/flags.rs` containing:
    *   A public `DenoFlags` struct with boolean fields: `help`, `log_debug`, `version`, `reload`, `allow_write`, and `allow_net`.
        *   Implement `PartialEq` for `DenoFlags`.
*   Implement the function `pub fn set_flags(args: Vec<String>) -> (DenoFlags, Vec<String>)`:
    *   Parse recognized flags from the argument list and map them to `DenoFlags` fields.
    *   Recognized flags:
        *   `-h` or `--help` sets `help=true`.
        *   `-D` or `--log-debug` sets `log_debug=true`.
        *   `-v` or `--version` sets `version=true`.
        *   `-r` or `--reload` sets `reload=true`.
        *   `--allow-write` sets `allow_write=true`.
        *   `--allow-net` sets `allow_net=true`.
    *   All fields in `DenoFlags` default to `false`.
    *   Return a tuple where the second element is a `Vec<String>` of non-flag arguments.
*   Implement the function `fn parse_core_args(args: Vec<String>) -> (Vec<String>, Vec<String>)`:
    *   Preprocess arguments for the V8 engine.
    *   Remove `--help` from `args_for_v8` and add it to `rest`.
    *   Replace `--v8-options` with `--help` in `args_for_v8` without adding it to `rest`.
*   Ensure the following test cases are implemented as embedded Rust test functions in `src/flags.rs`:
    *   `test_set_flags_1`: Verify `set_flags(["deno", "--version"])` results in `version=true` and `rest=["deno"]`.
    *   `test_set_flags_2`: Verify `set_flags(["deno", "-r", "-D", "script.ts"])` results in `reload=true`, `log_debug=true`, and `rest=["deno", "script.ts"]`.
    *   `test_set_flags_3`: Verify `set_flags(["deno", "-r", "script.ts", "--allow-write"])` results in `reload=true`, `allow_write=true`, and `rest=["deno", "script.ts"]`.
    *   `test_parse_core_args_1`: Verify `parse_core_args(["deno", "--v8-options"])` returns `(["deno", "--help"], [])`.
    *   `test_parse_core_args_2`: Verify `parse_core_args(["deno", "--help"])` returns `(["deno"], ["--help"])`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.