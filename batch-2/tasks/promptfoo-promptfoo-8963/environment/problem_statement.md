## Description

Several components in this codebase are using synchronous, blocking file system operations. In a Node.js server application, synchronous I/O calls block the entire event loop for the duration of the operation, degrading throughput and responsiveness. The affected areas include: the log export command, video provider caching logic shared across multiple providers, an SDK provider that creates and cleans up temporary directories, and a video file writing strategy.

## Expected Behavior

- The log export command should check for directory existence and read/archive log files using non-blocking, promise-based file system operations. On success, it should report the output file location.
- Video cache utility functions (computing cache paths, reading cache mappings, and storing cache mappings) should be consolidated into async helpers that never block the event loop. Reading a missing cache entry should return a null result rather than throwing. Writing a cache entry should ensure the target directory exists before writing.
- The SDK provider's cleanup of temporary directories should use async file removal.
- The video file writing strategy should use async file writes.

## Why This Matters

Synchronous file I/O prevents Node.js from processing other requests or events while the disk operation completes. Converting these calls to their async equivalents allows the runtime to remain responsive and is consistent with best practices for Node.js applications. Additionally, extracting the video cache helpers into a dedicated module with a clear async interface makes them independently testable and easier to reuse across multiple providers.
