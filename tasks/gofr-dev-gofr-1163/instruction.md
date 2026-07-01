Implement a terminal output utility within the GoFr framework to provide interactive feedback during long-running operations. This utility should include ANSI escape sequence helpers, animated spinners, and progress bars that adapt to terminal width. Ensure the utility integrates seamlessly with command handlers via the request context.

*   Create a terminal output utility in `pkg/gofr/cmd/terminal`:
    *   Implement `New()` to return a `*Out` with `os.Stdout`, `fd` as `uintptr(1)`, and `isTerminal` as `false` in non-terminal environments.
    *   Define `Out` struct embedding `terminal` struct, with fields `out io.Writer`, `fd uintptr`, and `isTerminal bool`.
    *   Implement ANSI escape sequence methods on `*Out` for screen and cursor control.
    *   Implement printing methods `Printf`, `Print`, and `Println` on `*Out`.

*   Define the `Output` interface in `pkg/gofr/cmd/terminal`:
    *   Include all public terminal control and print methods.
    *   Include an unexported `getSize() (int, int, error)` method.

*   Implement a progress bar component:
    *   Define `ProgressBar` struct with fields `stream Output`, `current int64`, `total int64`, `mu sync.Mutex`, and `tWidth int`.
    *   Implement `NewProgressBar(out Output, total int64) (*ProgressBar, error)` to handle terminal size retrieval and validation.
    *   Define `ProgressBar.Incr(n int64) bool` to update progress and output a formatted string.
    *   Implement `ProgressBar.getString() string` for progress display logic.

*   Implement spinner components:
    *   Define `Spinner` struct with fields `frames []string`, `fps time.Duration`, `outStream Output`, `started bool`, and `ticker *time.Ticker`.
    *   Implement spinner constructors: `NewDotSpinner`, `NewGlobeSpinner`, and `NewPulseSpinner`.
    *   Implement `Spin(ctx context.Context)` and `Stop()` methods for spinner control.

*   Update GoFr framework for integration:
    *   Add `Out terminal.Output` field to `gofr.Context`.
    *   Add `out terminal.Output` field to `cmd` struct in `pkg/gofr`.

*   Create example subcommands in `examples/sample-cmd/main.go`:
    *   Implement `spinner(ctx *gofr.Context) (interface{}, error)` to demonstrate spinner usage.
    *   Implement `progress(ctx *gofr.Context) (interface{}, error)` to demonstrate progress bar usage.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.