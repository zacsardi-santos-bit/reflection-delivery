Address the error handling gaps in Flutter tools by implementing proper error propagation for two specific scenarios.

*   Update the analyze command to handle analysis server crashes:
    *   When the analysis server process exits with a non-zero exit code, throw a `ToolExit` instead of a generic exception.
    *   Ensure the `ToolExit` message includes 'analysis server exited with code <N> and output:\n' followed by the server's captured output.
    *   Set the `exitCode` property of the `ToolExit` to match the server process's exit code.

*   Improve error handling for web debug service connection timeouts:
    *   Modify the `ResidentWebRunner`'s `run()` method to catch `TimeoutException` when connecting to the web debug service (DWDS).
    *   Throw a `ToolExit` with the message 'Failed to connect to the web debug service.' when a timeout occurs.
    *   Log an error message containing 'Failed to establish connection with the web debug service: ' followed by the exception's string representation, such as 'TimeoutException: Connection timed out'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.