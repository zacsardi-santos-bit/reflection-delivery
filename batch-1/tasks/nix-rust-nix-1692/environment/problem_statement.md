## Description

When working with network sockets in Rust using the nix library, there is currently no way to control whether IP packets should be fragmented. This makes it impossible to set the "don't fragment" flag on sockets through the existing socket option interface, which is a common need in network programming — for example, when implementing path MTU discovery or ensuring that packets traversing a network are never split into fragments.

## Expected Behavior

- Developers should be able to enable or disable the "don't fragment" flag on IPv4 sockets (on Apple platforms). This should work for both stream-oriented (TCP) and datagram-oriented (UDP) sockets.
- Developers should be able to enable or disable the "don't fragment" flag on IPv6 sockets (on Linux and Apple platforms). Again, this should work for both stream and datagram sockets.
- Both options should integrate naturally into the existing socket options workflow, accepting a boolean value and returning success when applied.

## Why This Matters

Many networking applications need fine-grained control over packet fragmentation to implement proper MTU probing or to guarantee packet delivery without splitting. Without this capability, developers are forced to use platform-specific unsafe code or workarounds. Adding these socket options fills a gap in the library's network programming coverage.
