Implement goroutine listing support in the Delve DAP server to handle requests for active goroutines properly. Update the client to start sequence numbering at 1 and suppress logging of raw protocol messages. Ensure the server returns appropriate responses based on the program's state.

*   Update the client sequence counter:
    *   Initialize the sequence counter to 1 in the `NewClient` function in `service/dap/daptest/client.go`.

*   Implement client methods:
    *   `ThreadsRequest`: Send a DAP "threads" request to the server.
        *   Location: `service/dap/daptest/client.go`
        *   Signature: `(c *Client) ThreadsRequest()`
    *   `StackTraceRequest`: Send a DAP "stackTrace" request to the server.
        *   Location: `service/dap/daptest/client.go`
        *   Signature: `(c *Client) StackTraceRequest()`
    *   `ExpectThreadsResponse`: Read and return the next server message as a `*dap.ThreadsResponse`.
        *   Location: `service/dap/daptest/client.go`
        *   Signature: `(c *Client) ExpectThreadsResponse(t *testing.T) *dap.ThreadsResponse`
    *   `ExpectStackTraceResponse`: Read and return the next server message as a `*dap.StackTraceResponse`.
        *   Location: `service/dap/daptest/client.go`
        *   Signature: `(c *Client) ExpectStackTraceResponse(t *testing.T) *dap.StackTraceResponse`

*   Modify the client's `send()` method:
    *   Ensure it does not print the JSON-serialized request to stdout.

*   Implement server-side handling for 'threads' requests:
    *   When no goroutines are available, return a response with 1 thread having `Id=1` and `Name="Dummy"`.
    *   When the process has exited, return a response with an empty threads list.
    *   When goroutines are available, return a response with each thread's `Id` set to the goroutine ID and `Name` set to the current function name.
    *   Ensure the response contains at least 2 threads when stopped at a breakpoint.

*   Maintain stack trace request behavior:
    *   Continue returning an error response with `Message="Unsupported command"` for stack trace requests.

*   Adjust sequence count tracking:
    *   Start the sequence count at 1 for bad-launch error response validation to match the corrected 1-based numbering.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.