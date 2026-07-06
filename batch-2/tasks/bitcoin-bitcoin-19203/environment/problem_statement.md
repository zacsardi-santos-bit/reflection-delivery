## Description

The SOCKS5 proxy handshake implementation in the Bitcoin networking layer is not independently testable because it is tightly coupled to live socket operations. There is no way to exercise the protocol logic in isolation — with arbitrary or adversarial socket responses — to verify that it handles all edge cases correctly.

A security vulnerability was discovered in 2017 that affected exactly this code path. Had fuzz testing been available at the time, it would have caught the issue within seconds. We should add the infrastructure needed to fuzz this code so that similar vulnerabilities cannot slip through undetected in the future.

## Expected Behavior

- The SOCKS5 handshake logic should be exposed as a standalone function that accepts a socket abstraction as a parameter, rather than always operating on a live network socket.
- The socket abstraction should be designed with virtual methods so that a fake, fuzz-driven implementation can be substituted during testing.
- A credential structure for proxy authentication (username and password) should be defined and accepted by the handshake function.
- A global timeout variable used during the handshake should be externally accessible so that timeout-related code paths can be exercised during fuzzing.
- A fuzz target for the SOCKS5 handshake should be wired up and runnable, exercising all combinations of hostname, port, optional credentials, and arbitrary socket I/O responses including errors, partial reads, and timeouts.

## Why This Matters

Fuzzing is a proven technique for discovering memory safety and security bugs in network protocol parsers. Making the SOCKS5 handshake fuzzable ensures that future changes to this code are continuously validated against a wide space of inputs, preventing the class of vulnerabilities that previously went undetected.
