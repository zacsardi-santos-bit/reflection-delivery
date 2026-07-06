## Description

When running the standalone query utility with verbose logging enabled, if a query fails, the log messages that were buffered before the failure are silently discarded. Only the exception is shown to the user — all the diagnostic context that was generated during query analysis and setup is lost.

## Expected Behavior

- When logging is enabled and a query fails, all log messages produced before the failure should appear in the output alongside the exception.
- Users should be able to see both the buffered log output (e.g., access checks, query parsing steps) and the error that caused the failure.

## Current Behavior

- When a query throws an exception, the exception is delivered immediately without first flushing the accumulated log buffer.
- This means any log messages generated before the failure — which could include valuable diagnostic information — are never printed.

## Why This Matters

Without the log output, it's very difficult to diagnose why a query failed. The log messages produced during query analysis and access checks provide important context. Losing them silently makes troubleshooting significantly harder.

This is a regression where the exception delivery path bypasses the log flush step, leaving the log queue non-empty and unprinted.
