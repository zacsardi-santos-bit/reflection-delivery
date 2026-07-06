I'm working with the MediaMTX RTSP static source and running into two related problems.

*   The RTSP static source must accept source URLs with the following schemes: rtsp://, rtsps://, rtsp+http://, rtsps+http://, rtsp+ws://, rtsps+ws://.

*   When the source URL scheme is rtsp+http:// or rtsps+http://, the outgoing connection to the RTSP server must use HTTP tunneling (gortsplib.TunnelHTTP).

*   When the source URL scheme is rtsp+ws:// or rtsps+ws://, the outgoing connection to the RTSP server must use WebSocket tunneling (gortsplib.TunnelWebSocket).

*   When the source URL scheme is rtsps://, rtsps+http://, or rtsps+ws://, the RTSP requests sent to the server must use the 'rtsps' URL scheme, and TLS must be configured using the SourceFingerprint from the source configuration.

*   When the source URL scheme is rtsp://, rtsp+http://, or rtsp+ws://, the RTSP requests sent to the server must use the 'rtsp' URL scheme, and TLS must NOT be configured.

*   Plain RTSP connections (rtsp:// with udp or tcp transport) must continue to work correctly and must not have TLS configuration applied to them.


*   Interface details: Type: Method
Name: Run
Location: internal/staticsources/rtsp/source.go
Signature: Run(params defs.StaticSourceRunParams) error
Description: Runs the RTSP static source. This method must parse the URL scheme from params.ResolvedSource and use it to configure the gortsplib.Client correctly. It must set c.Tunnel to gortsplib.TunnelHTTP for rtsp+http and rtsps+http schemes, set c.Tunnel to gortsplib.TunnelWebSocket for rtsp+ws and rtsps+ws schemes, normalize the URL scheme to "rtsp" for non-secure variants and "rtsps" for secure variants, and only apply TLS configuration (c.TLSConfig) when the scheme is a secure variant (rtsps, rtsps+http, rtsps+ws). The fields c.Scheme and c.Host must be set from the normalized, parsed URL after tunnel and scheme normalization are determined.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.