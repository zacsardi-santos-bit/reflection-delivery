Implement a low-level socket API in txiki.js to enable direct interaction with POSIX sockets from JavaScript. This API should provide functionalities for creating, configuring, and managing sockets, as well as utility functions for networking tasks.

*   Expose the `PosixSocket` class on the `tjs` global object as `tjs.PosixSocket`.
*   Implement the `PosixSocket` constructor to accept three numeric arguments: `domain`, `type`, and `protocol`, corresponding to POSIX `socket(2)` parameters.
*   Ensure `PosixSocket.defines` is a static object containing constants such as `AF_INET`, `SOCK_DGRAM`, `SOCK_STREAM`, `SOL_SOCKET`, `SO_REUSEADDR`, and `SO_BINDTODEVICE` with their standard POSIX numeric values.
*   Implement `PosixSocket.createSockaddrIn(ip, port)` to encode an IPv4 address and port into a 16-byte `Uint8Array` representing a `sockaddr_in` structure.
    *   On Linux, start with bytes `0x02, 0x00`; on macOS, `0x00, 0x02`.
    *   Encode port 12345 as bytes `[0x30, 0x39]` and port 55678 as `[0xd9, 0x7e]` at offsets 2-3.

*   Implement socket methods:
    *   `bind(sockaddrBytes)` to bind the socket using `bind(2)`.
    *   `sendmsg(addr, control, flags, ...buffers)` to send data to a specified address, returning the byte count.
    *   `recvmsg(bufsize, flags)` to receive data, returning an object with `data` and `addr`.
    *   `setopt(level, optname, optval)` to set socket options using `setsockopt(2)`.
    *   `getopt(level, optname, len)` to retrieve socket options, throwing an error for invalid parameters.
    *   `listen(backlog)` to mark the socket as passive using `listen(2)`.
    *   `accept()` to accept connections, returning a new socket object with `write(buf)` and `close()` methods.
    *   `write(buf)` to send data on a connected socket, returning the byte count.
    *   `fileno` as a read-only property returning the socket's file descriptor.
    *   `poll(callbacks)` to start polling for I/O events, invoking `callbacks.read` when readable.
    *   `stopPoll()` to cease polling.
    *   `close()` to close the socket.

*   Implement utility functions:
    *   `PosixSocket.nametoindex(name)` to convert interface names to numeric indices.
    *   `PosixSocket.indextoname(index)` to convert indices back to interface names.
    *   `PosixSocket.checksum(data)` to compute the IP checksum of a `Uint8Array`.

*   Ensure the entire `PosixSocket` API is a no-op on Windows, with no errors thrown.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.