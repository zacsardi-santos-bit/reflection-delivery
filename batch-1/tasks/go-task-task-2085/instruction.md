Refactor the executor creation process by implementing a constructor function that accepts a variadic list of option functions. This will allow the configuration of an executor with sensible defaults and simplify the initialization process by encapsulating configuration details.

*   Implement a constructor function `NewExecutor` in `executor.go`:
    *   Accepts zero or more `ExecutorOption` values.
    *   Returns a `*Executor` configured with sensible defaults (e.g., `os.Stdin/Stdout/Stderr` for I/O).
*   Define `ExecutorOption` as a function type with the signature `func(*Executor)`.

*   Provide the following option functions in `executor.go`:
    *   `ExecutorWithDir(string) ExecutorOption` to set the working directory.
    *   `ExecutorWithStdout(io.Writer) ExecutorOption` and `ExecutorWithStderr(io.Writer) ExecutorOption` to set the standard output and error writers.
    *   `ExecutorWithStdin(io.Reader) ExecutorOption` to set the standard input reader.
    *   `ExecutorWithEntrypoint(string) ExecutorOption` to set the entrypoint (main Taskfile path).
    *   `ExecutorWithTempDir(TempDir) ExecutorOption` to configure the temporary directory.
    *   `ExecutorWithSilent(bool) ExecutorOption`, `ExecutorWithVerbose(bool) ExecutorOption`, `ExecutorWithDry(bool) ExecutorOption`, `ExecutorWithSummary(bool) ExecutorOption` to set mode flags.
    *   `ExecutorWithForce(bool) ExecutorOption` and `ExecutorWithForceAll(bool) ExecutorOption` to control task execution conditions.
    *   `ExecutorWithInsecure(bool) ExecutorOption`, `ExecutorWithDownload(bool) ExecutorOption`, `ExecutorWithOffline(bool) ExecutorOption`, `ExecutorWithTimeout(time.Duration) ExecutorOption` for network settings.
    *   `ExecutorWithAssumeYes(bool) ExecutorOption` to automatically accept prompts.
    *   `ExecutorWithConcurrency(int) ExecutorOption` to set task concurrency.
    *   `ExecutorWithVersionCheck(bool) ExecutorOption` to enable Taskfile schema-version validation.
    *   `ExecutorWithOutputStyle(ast.Output) ExecutorOption` to set the output style.
    *   `ExecutorWithWatch(bool) ExecutorOption` to enable watch mode.

*   Ensure the `Executor` struct retains the following directly settable fields:
    *   `Dir` (string)
    *   `AssumeTerm` (bool)
    *   `UserWorkingDir` (string)

*   Maintain functionality of existing `Executor` methods (`Setup`, `Run`, `Status`, `ListTasks`, `ListTaskNames`) when using `NewExecutor`.
*   Enable verbose output with `ExecutorWithVerbose(true)` to replace direct logger configuration, eliminating the need to import the internal logger package.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.