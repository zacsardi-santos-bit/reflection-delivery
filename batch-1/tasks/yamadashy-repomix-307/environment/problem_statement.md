## Description

Processing large repositories is taking too long. When a repository has hundreds or thousands of files, the tool currently handles file collection, content processing, security scanning, and token counting sequentially on the main thread — which doesn't take advantage of modern multi-core hardware and can become noticeably slow.

We should refactor these computationally intensive operations to run in parallel using a background worker thread pool. Each file should be handed off to a worker thread rather than processed one-at-a-time, allowing multiple files to be processed concurrently across all available CPU cores.

## Expected Behavior

- File collection, file processing, security checks, and output metrics calculation should all be offloaded to worker threads
- Each operation type should have its own dedicated worker module that handles processing a single unit of work
- A utility should determine the appropriate number of worker threads based on the number of available CPU cores and the number of tasks to process — scaling up threads for larger workloads, capping at the CPU count, and using a minimum of 1 thread
- The function that aggregates all per-file metrics should accept a token encoding string directly rather than a pre-constructed token counter object, since the token counter will now be managed inside the worker
- A dedicated function for computing the token count of the final output string should be introduced, also running in a worker thread
- Security check logic (secret detection configuration and per-file linting) should be moved into its own worker module
- The security check runner should no longer rely on a separate "if enabled" wrapper — the enabled/disabled check should be inlined at the call site

## Why This Matters

For developers working with large codebases, processing time is a significant pain point. Leveraging multiple cores through worker threads should dramatically reduce wall-clock time for large repositories without changing the observable output.
