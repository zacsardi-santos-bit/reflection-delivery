I'm working with Tokio for async I/O on Unix systems, and I need async support for Unix named pipes (FIFOs). Right now there's no way to open a FIFO file and read or write to it asynchronously through Tokio — I have to fall back to blocking I/O, which doesn't play well with the rest of my async code.

What I'd like is a module that exposes at least two types — one for the reading end and one for the writing end of a named pipe — along with an options builder that lets you open each end by path. The builder should validate that the path actually points to a FIFO, not just any file. Both the reading and writing types should support the standard async read and write traits, plus non-blocking try-read and try-write methods (including vectored variants), and async methods for waiting until the pipe is readable or writable.

I also want to be able to construct these types from an already-open standard-library file handle, with the library automatically setting non-blocking mode and checking both that the file is a FIFO and that it was opened with the right access mode (read for the receiver, write for the sender).

On Linux specifically, it would be great to have a mode where a sender can be opened even without an active reader already connected (normally that returns an OS-level error), and a mode where a receiver doesn't get an end-of-file when all writers disconnect — instead it stays open and can asynchronously wait for the next writer to connect.

All the types and their async futures should be safe to send and share across threads.
