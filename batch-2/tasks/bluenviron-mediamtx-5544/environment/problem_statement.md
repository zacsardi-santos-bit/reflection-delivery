## Description

The RTSP source reader in MediaMTX does not reliably support the full range of RTSP URL schemes for pulling streams from remote servers. Specifically, HTTP-tunneled and WebSocket-tunneled connections — which are essential in firewall- or proxy-restricted environments — are not working correctly. Furthermore, there appears to be a bug where encryption settings are applied to all connections regardless of the URL scheme, causing even plain unencrypted connections to break in certain configurations.

## Expected Behavior

- When a source URL uses the HTTP-tunneling variant, the connection to the remote RTSP server must use HTTP tunneling.
- When a source URL uses the WebSocket-tunneling variant, the connection must use WebSocket tunneling.
- When a source URL uses a secure variant (i.e., the scheme signals encryption), outgoing requests must use the secure scheme, and TLS must be properly configured using the provided fingerprint.
- When a source URL uses a plain (non-secure) variant, no TLS should be applied, regardless of other settings.
- Existing plain UDP and TCP transports must continue to work correctly.

## Why This Matters

Many real-world RTSP cameras and servers are only reachable via HTTP or WebSocket tunneling because firewalls block standard RTSP ports. Users trying to ingest such streams into MediaMTX currently cannot do so reliably. Additionally, the encryption bug means that even users with working plain RTSP setups may encounter unexpected failures. Supporting these URL scheme variants directly — without requiring additional configuration — makes MediaMTX usable in a much wider range of network environments.
