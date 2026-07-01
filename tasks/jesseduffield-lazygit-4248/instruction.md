Update the dependencies for the goroutine ID lookup and deadlock detection libraries to ensure compatibility with the latest Go runtime. Ensure that the goroutine ID returned matches the Go runtime's reported ID.

*   Update the goroutine ID library:
    *   Modify `go.mod` and `go.sum` to set `github.com/petermattis/goid` to version `v0.0.0-20240813172612-4fcff4a6cae7`.
    *   Replace the vendor directory entry for `github.com/petermattis/goid` with files from the updated version.
        *   Ensure inclusion of a `runtime_go1.23.go` file that defines the correct goroutine struct layout for Go 1.23.

*   Update the deadlock detection library:
    *   Modify `go.mod`, `go.sum`, and the vendor directory to set `github.com/sasha-s/go-deadlock` to version `v0.3.5`.

*   Ensure functionality:
    *   Verify that `goid.Get()` returns an `int64` value matching the goroutine ID from the Go runtime's stack trace output.
    *   Update `vendor/modules.txt` to reflect the new versions of both libraries.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.