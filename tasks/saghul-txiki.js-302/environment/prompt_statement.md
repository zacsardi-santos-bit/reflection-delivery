I'm working with txiki.js and I need a way to perform low-level socket operations directly from JavaScript. Right now the runtime only exposes higher-level networking primitives, but I need to be able to create sockets at the OS level, configure socket options, and work with the raw binary address structures the OS expects.

Specifically, I'd like to be able to create datagram and stream sockets by specifying address family, type, and protocol; have access to the standard socket-level constants I'd normally use in C (address families, socket types, option levels and names); and have a way to encode IPv4 addresses and ports into the binary sockaddr format. For sending and receiving on datagram sockets, I need the receive side to give me back both the data and the sender's address. For stream sockets, I need to be able to bind, listen, accept connections, and write data to those connections.

I also need to be able to set and get socket options — and the option retrieval operation should raise an error if I pass an invalid level, a zero-length buffer, or an unsupported option. I'd like access to the raw file descriptor number of the socket, and I need a way to poll a socket for readability and stop that poll later.

Beyond the core socket operations, it would be great to have some helper utilities: a way to convert between network interface names and their numeric indices (which should round-trip correctly for all interfaces the runtime can enumerate), and a function to compute IP checksums over raw byte arrays.

This functionality is only needed on POSIX platforms — on Windows it's fine for the whole thing to be a no-op.
