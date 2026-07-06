# Email Validation Should Throw Recoverable Errors Instead of Exiting

## Description

Right now, when email validation fails at runtime — for example when the user has exceeded their cloud inference limit or hasn't verified their email address — the system calls `process.exit()` directly. This is too aggressive: it makes it impossible for any caller (such as a file watcher or an embedding application) to recover or handle the failure gracefully.

Similarly, if the user presses Ctrl+C to cancel the email prompt, the process exits without giving the caller a chance to clean up or continue.

## Expected Behavior

- Email validation failures should be signalled by throwing a dedicated, catchable error type rather than terminating the process immediately.
- Commands that trigger email validation should catch these specific errors, record an appropriate exit code, and suppress redundant error output — since the underlying validation code already logs the relevant message.
- A file watcher that encounters an email validation error on a config reload should continue watching rather than crashing.
- When an operation that sets up a log callback fails, the log callback should be cleaned up safely — even if the operation that registered it throws.
- A utility should exist to clear a log callback only when the caller that registered it is still the active owner, preventing one operation from inadvertently clearing a callback registered by another.

## Why This Matters

Hard process exits prevent any recovery logic from running and make the tool unusable as a library or inside automated pipelines. Replacing exits with thrown errors lets each command decide how to respond — setting an exit code, logging a user-friendly message, or simply continuing — without compromising the user experience.
