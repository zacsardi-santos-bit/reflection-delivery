I'm doing a bunch of async I/O with Tokio on Unix and I keep hitting a wall with named pipes (FIFOs). There's just no way right now to open a FIFO and read or write it asynchronously through Tokio, so I fall back to blocking I/O and it doesn't mesh with the rest of my async code at all. Named pipes are a totally standard IPC mechanism on Unix so it feels like a real gap next to sockets and everything else.

What I want is a new module (something living under the unix net area, like `@tokio/src/net/unix/pipe.rs`) that gives me at least two types, one for the reading end and one for the writing end. Plus an options builder to open either end by path, and it should actually validate that the path points to a FIFO and not just some random file, returning an invalid-input error if it isn't.

Both ends should implement the standard async read/write traits. The receiver needs async reading with readiness polling, non-blocking try-read including vectored and buffer-based reads, plus a way to async-wait until it's readable, and it should signal EOF properly when all writers disconnect. The sender side mirrors that: async writing, poll for writability, non-blocking try-write including vectored, and an async wait-until-writable.

I also want to build either end from an already-open std file handle, where the library flips it to non-blocking automatically and checks both that it's really a FIFO and that it was opened with the right access mode (read for the receiver, write for the sender), erroring otherwise.

On Linux specifically it'd be great to have a mode where I can open a sender even without a reader connected yet (normally that gives an OS error like ENXIO), and a mode where a receiver doesn't hit EOF when all writers disconnect, it just stays open and can async-wait for the next writer to show up.

Oh and everything, the types and all their async futures, needs to be Send + Sync so I can move them across threads.
