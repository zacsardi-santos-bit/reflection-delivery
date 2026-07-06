I'm using the standalone ClickHouse query utility with verbose logging turned on, and I've noticed that when a query fails, I only see the exception — none of the log messages that were generated before the failure appear in the output.

*   When clickhouse-local is run with a logging level set (e.g., trace) and a query fails, all log messages accumulated before the failure must be printed to stderr before the exception is reported — they must not be discarded.

*   When clickhouse-local runs a query that fails with an UNKNOWN_TABLE error and logging is enabled at the trace level, the stderr output must contain both Debug or Trace level log lines AND the UNKNOWN_TABLE error information.

*   In src/Client/LocalConnection.cpp, the poll() method must check whether there are buffered log messages pending (via needSendLogs()) before delivering the exception packet, and if so, defer the exception until the buffered logs have been sent.


*   Interface details: Type: Function
Name: poll
Location: src/Client/LocalConnection.cpp
Signature: poll(size_t timeout_microseconds) -> bool
Description: Polling method on LocalConnection that checks for pending work. Must be modified to check whether there are buffered log messages pending (via needSendLogs()) before delivering an exception packet. If buffered logs are pending when an exception state is detected, the method must return true (indicating work is pending) without yet setting the exception packet type, so that logs are flushed first.

Type: Function
Name: needSendLogs
Location: src/Client/LocalConnection.cpp
Signature: needSendLogs() -> bool
Description: Existing helper that returns true if there are buffered log messages waiting to be sent. Must be called inside poll() before the exception packet is set, to ensure logs are flushed before the exception is delivered to the client.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.