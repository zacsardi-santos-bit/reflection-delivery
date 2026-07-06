Implement async support for Unix named pipes (FIFOs) in the Tokio library. Create a module that provides types for handling the reading and writing ends of a named pipe, along with an options builder for opening these ends by path. Ensure the module integrates seamlessly with the async ecosystem and validates FIFO paths.

*   Create a new public module `tokio::net::unix::pipe` exporting `OpenOptions`, `Sender`, and `Receiver`.
*   Ensure `OpenOptions`, `Sender`, and `Receiver` implement `Send`, `Sync`, and `Unpin`.
    *   Futures returned by `Receiver::readable`, `Receiver::ready`, `Sender::ready`, and `Sender::writable` must implement `Send` and `Sync`, but not `Unpin`.
*   Implement `OpenOptions` with:
    *   Constructor: `OpenOptions::new() -> OpenOptions`.
    *   Builder methods:
        *   `open_receiver(path: impl AsRef<Path>) -> io::Result<Receiver>`.
        *   `open_sender(path: impl AsRef<Path>) -> io::Result<Sender>`.
        *   Return `io::ErrorKind::InvalidInput` if the path is not a FIFO.
    *   On Linux, `open_sender` must fail with `ENXIO` if no reader is connected, unless `read_write(true)` is set.
    *   `read_write(bool)` option to allow opening senders without readers and receivers that remain open across writer disconnections.
*   Implement `Receiver`:
    *   Must implement `AsyncRead`, `Send`, `Sync`, `Unpin`, and `AsRawFd`.
    *   Provide methods:
        *   `from_file(file: std::fs::File) -> io::Result<Receiver>`.
        *   `ready(&self, interest: Interest) -> impl Future<Output = io::Result<Ready>>`.
        *   `readable(&self) -> impl Future<Output = io::Result<()>>`.
        *   Non-blocking read methods: `try_read`, `try_read_vectored`, `try_read_buf`.
        *   Return `io::ErrorKind::WouldBlock` when no data is available.
    *   Standard receivers must receive EOF when all writers disconnect.
*   Implement `Sender`:
    *   Must implement `AsyncWrite`, `Send`, `Sync`, `Unpin`, and `AsRawFd`.
    *   Provide methods:
        *   `from_file(file: std::fs::File) -> io::Result<Sender>`.
        *   `ready(&self, interest: Interest) -> impl Future<Output = io::Result<Ready>>`.
        *   `writable(&self) -> impl Future<Output = io::Result<()>`.
        *   Non-blocking write methods: `try_write`, `try_write_vectored`.
        *   Return `io::ErrorKind::WouldBlock` when the pipe buffer is full.
*   Ensure `Receiver::from_file` and `Sender::from_file` validate file types and access modes, setting non-blocking mode automatically.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.