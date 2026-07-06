## Description

When viewing task logs in Airflow using CloudWatch as the remote log storage backend, if the task's log stream doesn't exist — for example because the task wrote to standard output instead of remote storage, or hasn't produced any logs yet — the log reader crashes with an unhandled exception. This surfaces as a server error (500) for the user, or in some cases shows a completely blank log view that makes it look like remote logging silently failed.

## Expected Behavior

- When the log stream does not exist in CloudWatch, the log reader should gracefully return a single informational message explaining that no log stream was found, rather than raising an exception. The message should include the name of the log stream that was looked up.
- When a genuine error occurs (such as an access or permissions problem), the error should still propagate normally so operators are not misled about the root cause.

## Why This Matters

Currently, users who encounter a missing log stream (a fairly common scenario when tasks haven't started writing or log to stdout only) get a cryptic error or a blank page instead of a useful explanation. A clear hint message would help operators quickly understand what happened and distinguish this from a misconfiguration.
