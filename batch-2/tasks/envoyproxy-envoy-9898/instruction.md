Implement a shared, reusable synchronous UDP peer helper class to streamline UDP socket management in tests and enhance the statsd sink functionality to support Unix domain socket addresses. Update existing tests to utilize the new helper and verify datagram content.

*   Create the `UdpSyncPeer` class in the `Network::Test` namespace:
    *   Add to `test/test_common/network_utility.h` and implement in `test/test_common/network_utility.cc`.
    *   Constructor: Accept `Network::Address::IpVersion` to create a blocking UDP socket on the loopback address for the specified IP version.
    *   Implement `write(const std::string& buffer, const Network::Address::Instance& peer)` to send a UDP datagram to the specified peer address, ensuring all bytes are sent.
    *   Implement `recv(Network::UdpRecvData& datagram)` to synchronously receive a UDP datagram and populate the provided `datagram` object.
    *   Implement `localAddress()` to return a constant reference to the socket's local address.

*   Update the `UdpStatsdSink` class:
    *   Add a nested abstract class `Writer` in `source/extensions/stat_sinks/common/statsd/statsd.h`:
        *   Inherit from `ThreadLocal::ThreadLocalObject`.
        *   Declare a pure virtual method `write(const std::string& message)`.
    *   Ensure `UdpStatsdSink` can be initialized with a Unix domain socket address.
    *   Implement graceful failure for flush operations when no server is listening.
    *   Ensure that once a UDS server is bound and listening, flush sends correctly-formatted statsd datagrams.

*   Update statsd sink tests:
    *   Verify actual wire content of sent metric datagrams for counters, gauges, and histograms.
    *   Ensure datagrams are formatted correctly, appending tags when present.

*   Refactor existing tests:
    *   Replace any locally-defined synchronous UDP client classes in `udp_proxy_integration_test.cc` with `UdpSyncPeer`.
    *   Remove the local `UdpSyncClient` class from `udp_proxy_integration_test.cc`.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.