## Description

The library currently lacks support for Linux's filesystem event monitoring subsystem. This makes it impossible for Rust developers who rely on this library to observe what happens on the filesystem — such as file creations, modifications, renames, and deletions — within directories they care about. These capabilities are available at the OS level on Linux and Android, but are not yet exposed through a safe Rust interface in this library.

## Expected Behavior

- Developers should be able to create a filesystem watcher instance, optionally in non-blocking mode.
- Developers should be able to register directories (or files) to watch, specifying which categories of events are of interest.
- When no events are available and the watcher is in non-blocking mode, attempting to read events should return a "try again" error rather than blocking.
- After filesystem activity occurs, reading from the watcher should return a list of structured event records, each containing:
  - The type of event that occurred (creation, open, close, move, etc.)
  - The name of the affected file within the watched directory
  - A correlation token that links related move events together (so the source and destination of a rename can be associated)
- Events should be returned in the order they occurred and should accurately reflect the sequence of filesystem operations.

## Why This Matters

Without this feature, developers who need to react to filesystem changes in their Linux/Android applications must use unsafe FFI calls directly, bypassing the safe wrappers that this library is meant to provide. Adding this support makes a widely-used OS capability accessible through a safe, idiomatic interface.
