Implement the ability to set the "don't fragment" flag on IP sockets using the nix Rust library. This involves adding new socket options for both IPv4 and IPv6 that can be toggled with a boolean value. Ensure these options integrate seamlessly into the existing socket options workflow.

*   Add an `IpDontFrag` socket option to the `sockopt` module:
    *   Implement in `src/sys/socket/sockopt.rs`.
    *   Conditionally compile for iOS and macOS using `#[cfg(any(target_os = "ios", target_os = "macos"))]`.
    *   Use the `sockopt_impl!` macro with `Both` access mode.
    *   Map to `libc::IPPROTO_IP` level and `libc::IP_DONTFRAG` constant.
    *   Accept boolean values to enable (`true`) or disable (`false`) fragmentation prevention.
    *   Ensure `setsockopt` succeeds without error on both IPv4 stream (TCP) and datagram (UDP) sockets.

*   Add an `Ipv6DontFrag` socket option to the `sockopt` module:
    *   Implement in `src/sys/socket/sockopt.rs`.
    *   Conditionally compile for Android, iOS, Linux, and macOS using `#[cfg(any(target_os = "android", target_os = "ios", target_os = "linux", target_os = "macos"))]`.
    *   Use the `sockopt_impl!` macro with `Both` access mode.
    *   Map to `libc::IPPROTO_IPV6` level and `libc::IPV6_DONTFRAG` constant.
    *   Accept boolean values to enable (`true`) or disable (`false`) fragmentation prevention.
    *   Ensure `setsockopt` succeeds without error on both IPv6 stream (TCP) and datagram (UDP) sockets.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.