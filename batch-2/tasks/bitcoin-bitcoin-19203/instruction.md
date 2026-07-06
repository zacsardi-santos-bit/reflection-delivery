Refactor the SOCKS5 proxy handshake implementation in Bitcoin Core to enable fuzz testing. Decouple the handshake logic from live socket operations by introducing a socket abstraction. Implement fuzz testing infrastructure to ensure the handshake logic handles all edge cases and potential vulnerabilities.

*   Define the `ProxyCredentials` struct in `src/netbase.h`:
    *   Include a `username` field of type `std::string`.
    *   Include a `password` field of type `std::string`.

*   Implement a global integer variable `g_socks5_recv_timeout`:
    *   Define it in `src/netbase.cpp`.
    *   Ensure it is accessible externally via an `extern` declaration.
    *   Allow its value to be modified at runtime to control the SOCKS5 handshake receive timeout.

*   Refactor the `Socks5` function:
    *   Declare it in `src/netbase.h` and define it in `src/netbase.cpp`.
    *   Accept parameters: `const std::string& hostname`, `int port`, `const ProxyCredentials* auth`, and `const Sock& socket`.
    *   Return a `bool` indicating success or failure.
    *   Ensure it operates correctly for any combination of inputs, including arbitrary hostnames, port values, and socket I/O responses.

*   Verify the `InterruptSocks5` function:
    *   Ensure it accepts a `bool` parameter.
    *   Confirm it can be called from the fuzz harness to manage the interrupt flag for SOCKS5 operations.

*   Update the `Sock` class:
    *   Declare virtual methods: `Send`, `Recv`, `Wait`, `Get`, `Release`, and `Reset`.
    *   Allow `FuzzedSock` to override these methods for fuzz testing.

*   Add `src/test/fuzz/socks5.cpp` to the fuzz build target:
    *   Modify `src/Makefile.test.include` to include this file in the `test_fuzz_fuzz_SOURCES` list.
    *   Ensure the fuzz target is compiled into the fuzz binary and can be selected using `FUZZ=socks5`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.