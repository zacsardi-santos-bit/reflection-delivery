I'm working on a code repository packaging tool and I want to improve its performance by parallelizing the heavy processing steps. Right now, everything runs on the main thread — reading files, processing their content, running security checks, and counting tokens — and it gets noticeably slow on large repos.

I'd like to refactor the tool to use a worker thread pool so these operations can run concurrently across all available CPU cores. Each major operation (file collection, file content processing, per-file metrics, output metrics, security checks) should have its own dedicated worker module that handles a single unit of work, with the orchestrating function dispatching tasks to the pool.

I also need a utility that decides how many worker threads to spin up given the number of tasks and available CPUs — it should scale threads with task count, cap at the CPU count, and use at least 1 thread regardless. The function that computes per-file token counts should accept an encoding name directly instead of a pre-built counter object, since the counter will live inside the worker. A new separate function should handle token-counting for the final output string.

The security scanning logic (secret detection setup and per-file linting) should move into its own worker module. The outer security check orchestrator should handle parallelism and error reporting, while the conditional "run only if enabled" logic gets inlined at the call site rather than wrapped in a separate function.

All the orchestrating functions should accept optional dependency injection for their task runners so they remain testable.
