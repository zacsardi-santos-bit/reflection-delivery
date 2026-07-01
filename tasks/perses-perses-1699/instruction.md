Implement a new `build` subcommand for the CLI tool to compile dashboard definition files into YAML or JSON. Update the `apply` and `lint` commands to handle input flags consistently, ensuring mutual exclusivity and clear error messaging.

*   Implement the `build` subcommand:
    *   Create the `NewCMD` function in `internal/cli/cmd/dac/build/build.go` to define the `build` command.
    *   Support flags: `-f/--file` for a single CUE file, `-d/--directory` for a directory of CUE files, `-m/--mode` for output mode ("file" or "stdout", default "file"), and `-o/--output` for output format ("yaml" or "json", default "yaml").
    *   Ensure `-f` and `-d` flags are mutually exclusive using `MarkFileAndDirFlagsAsXOR`.
    *   Use the `outputFolderName` constant with the value "built" for storing output files.
    *   Print success message: `'Succesfully built <input-path> at built/<input-dir>/<basename>_output.yaml\n'` for each file processed.
    *   In `stdout` mode with `json` format, print evaluated content to standard output without creating files.
    *   Handle CUE file errors with message: `'failed to build <file>: <cue-eval-stderr>'`.

*   Update `apply` and `lint` commands:
    *   Accept either a `file` or `directory` flag, requiring exactly one.
    *   Return error `'at least one of the flags in the group [file directory] is required'` when neither flag is provided.
    *   Return error `'if any flags in the group [file directory] are set none of the others can be; [directory file] were all set'` when both flags are provided.

*   Implement validation methods:
    *   `FileOption.Validate()` in `internal/cli/opt/opt.go` to check file path existence, returning error `'invalid value set to the File flag: <os-error>'` if not found, or passing if `'-'`.
    *   `DirectoryOption.Validate()` in `internal/cli/opt/opt.go` to check directory path existence, returning error `'invalid value set to the Directory flag: <os-error>'` if not found.

*   Ensure test data includes:
    *   `working_dac.cue` as a valid dashboard definition file.
    *   `working_dac_2.cue` as a simple valid CUE file evaluating to `{"success": true}` in JSON.
    *   `invalid_dac.cue` as an invalid CUE file with an undefined symbol error.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.