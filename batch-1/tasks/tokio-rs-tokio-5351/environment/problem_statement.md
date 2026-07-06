# Add Async Unix Named Pipe (FIFO) Support

## Description

Tokio currently has no support for Unix named pipes (FIFOs). Developers who need to communicate between processes using FIFO files on Unix systems are forced to use blocking I/O or write their own complex wrappers, neither of which integrates cleanly with the async ecosystem.

This feature request is to add first-class async support for Unix named pipes, including both reading and writing ends, all the standard async readiness and non-blocking I/O methods, and a way to create pipe endpoints from existing file handles.

## Expected Behavior

- An options builder should allow opening either the reading or writing end of a named pipe file, and should reject paths that are not actually FIFO files (returning an appropriate invalid-input error).
- The reading end should implement async reading and expose methods for polling readiness, non-blocking reads (including vectored reads and buffer-based reads), and should properly signal end-of-file when all writers disconnect.
- The writing end should implement async writing and expose methods for polling writability and non-blocking writes (including vectored writes).
- Both ends should be constructible from existing standard-library file handles, which should be automatically configured for non-blocking operation and validated to ensure the correct file type and access mode.
- On Linux, there should be a way to open the writing end without requiring a reader to be connected first, and a way to open a reading end that remains open across writer disconnections without receiving an end-of-file signal.
- All types and their async methods should be safe to share across threads.

## Why This Matters

Named pipes are a standard Unix inter-process communication mechanism. Without async support in Tokio, applications that need to bridge blocking and async code around FIFOs become unnecessarily complex. Adding this support allows developers to work with FIFOs using the same ergonomic async patterns they use for sockets and other I/O resources.
