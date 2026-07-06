## Description

Several executor and utility functions in the monorepo tooling are currently synchronous or return only a single result, which limits their ability to stream multiple results over time. This is especially problematic for watch-mode builds, which need to continuously emit build outcomes as files change. Additionally, the function that builds the project graph — a core piece of infrastructure used throughout the tooling — performs file I/O but is implemented synchronously, which is inconsistent with modern asynchronous design patterns. This inconsistency cascades into other utilities (like the one that checks project dependencies before removal) which must also become asynchronous to properly retrieve the project graph.

## Expected Behavior

- The project graph creation utility should be available as a proper asynchronous function, returning a result that callers can wait for non-blockingly.
- Executor functions for building Angular libraries and Node applications should return a stream of successive build results over time, allowing callers to iterate over them.
- In non-watch mode, consuming the executor's results once should produce a single result indicating whether the build succeeded or failed, and the sequence should then be complete with no more results to follow.
- In watch mode, the executor should keep emitting new results as changes occur, without requiring callers to perform special workarounds to iterate over its results.
- The dependency-checking utility used during project removal should be asynchronous, properly retrieving the project graph non-blockingly instead of calling a synchronous version.
- When a project has dependents and removal is not forced, the asynchronous dependency check should reject with an error naming all dependent projects; when forced or when no dependents exist, it should resolve cleanly.

## Why This Matters

These changes align the tooling with asynchronous-first design, enabling watch-mode executors to stream results progressively and ensuring that infrastructure functions like project graph creation don't block the event loop. Without these changes, watch mode cannot emit incremental updates and the project graph API is inconsistent with the non-blocking nature of file-system operations.
