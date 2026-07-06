## Description

Several test files that deal with UDP networking each independently implement their own low-level socket management: allocating a socket, toggling it into blocking mode, building raw buffer slices, and writing retry loops to read back responses. This duplicated boilerplate makes tests hard to read and maintain. One integration test file even has its own private synchronous UDP helper class that is invisible to the rest of the test suite.

Additionally, the statsd sink tests currently only verify internal state (file descriptor values and connected addresses) rather than checking that the correct data actually arrives at a UDP receiver. There is also no test coverage for the case where the sink is initialized with a Unix domain socket address — which should work, but at present the underlying implementation uses a connection-oriented approach that does not support non-IP socket types.

## Expected Behavior

- A shared, reusable synchronous UDP peer helper should exist in the common test infrastructure so any test can easily send and receive UDP datagrams without duplicating socket setup code.
- Existing tests that manage UDP sockets manually should be simplified to use this shared helper.
- The statsd sink should support being initialized with a Unix domain socket address and should degrade gracefully if no server is listening when a flush is attempted.
- The statsd sink tests should verify the actual wire content of sent metric datagrams: counters, gauges, and histograms with and without tags.

## Why This Matters

Centralizing UDP test plumbing reduces the chance of subtle bugs in test helpers and makes the tests themselves shorter and more readable. Validating actual datagram content (rather than file descriptor state) makes the tests meaningful regression guards. Supporting Unix domain sockets for statsd expands deployment flexibility.
