I'm working on a Rust systems library that wraps Linux system calls, and I'd like to add support for the Linux filesystem event monitoring interface. Right now there's no way to watch a directory for changes — things like file creation, renaming, or opens — using this library, which means developers have to resort to unsafe raw system calls themselves.

I need a safe, idiomatic Rust module that lets developers create a watcher instance (optionally in non-blocking mode), register directories for monitoring, and read back structured events describing what happened. Each event should include the type of operation, the name of the affected file, and a shared identifier that links related events together (for example, the two sides of a file rename should share a cookie so callers can match them up).

When the watcher is in non-blocking mode and no events are ready yet, the read call should signal that there's nothing to read rather than blocking. And when filesystem operations do happen, the events should come back in the correct sequence reflecting what actually occurred on the filesystem.

This should be exposed as a new module within the library's system-call wrapper layer, available on Linux and Android targets.
