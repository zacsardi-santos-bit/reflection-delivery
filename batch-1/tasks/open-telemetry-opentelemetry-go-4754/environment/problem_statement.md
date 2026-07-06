## Description

The OpenTelemetry Go SDK and its exporters are currently using an outdated version of the semantic conventions package. A newer version of the conventions has been released, which includes renamed and updated attribute keys for common telemetry data — particularly around network communication. The Zipkin exporter, which relies on span attributes to determine the remote endpoint of a trace, needs to be updated to recognize both the new attribute names and an expanded priority ordering.

## Expected Behavior

- All exporters and SDK components should reference the current semantic conventions version.
- The attribute used to identify an HTTP response status code should match the current specification name.
- The network peer address and port attributes should use their current names from the updated specification.
- The Zipkin exporter should correctly extract the remote endpoint from spans using new-style attribute names, with the following priority (highest to lowest): explicit peer service name, server address (name), legacy peer name, network peer address (IP), server socket domain (name), server socket address (IP), legacy socket peer name, legacy socket peer address (IP), peer hostname, peer address (IP), database name.
- Port selection for IP-based endpoint resolution must be matched to the correct attribute based on which IP source was used.
- Invalid IP values must not produce a remote endpoint; IPv6 addresses must be correctly handled.

## Why This Matters

Users building on the OpenTelemetry Go SDK expect that it references current, non-deprecated attribute names. When a user exports trace data to Zipkin and their spans include the latest standard network attributes, the Zipkin exporter should correctly identify and export the remote endpoint rather than silently dropping or misidentifying it.
