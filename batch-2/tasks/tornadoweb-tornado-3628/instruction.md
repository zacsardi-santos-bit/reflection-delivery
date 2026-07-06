I've identified a few security and correctness bugs in the Tornado framework that need fixing:

First, when the HTTP client automatically follows redirects, it's forwarding sensitive authentication credentials to the redirect destination without checking whether the destination is the same origin as the original request.

*   When the HTTP client follows a redirect to a different origin (different scheme or netloc, including a different port), it must remove the Authorization and Cookie headers from the redirected request. If the original URL contained embedded credentials (username/password in the URL), those must also be stripped from the redirect URL. Same-origin redirects must preserve Authorization and Cookie headers. Non-authentication headers such as User-Agent must not be stripped on any redirect.

*   The gzip decompression path in the HTTP server must enforce the configured max_body_size limit against the decompressed body size, not just the compressed size. If the total decompressed bytes received exceed max_body_size, the server must raise an HTTPInputError with the message 'decompressed body too large', which will be logged at INFO level.

*   The _GzipMessageDelegate class in tornado/http1connection.py must accept a max_body_size integer parameter and track the cumulative decompressed byte count across chunks. When any chunk causes the running total to exceed max_body_size, it must immediately raise httputil.HTTPInputError('decompressed body too large').

*   The _websocket_mask_python function in tornado/util.py must validate that the mask argument is exactly 4 bytes long. If len(mask) != 4, it must raise ValueError with the message 'mask must be 4 bytes' before performing any masking operation.

*   The C extension websocket_mask function in tornado/speedups.c must validate that the mask argument is exactly 4 bytes long. If the mask length is not 4, it must raise ValueError with the message 'mask must be 4 bytes' before performing any masking operation.

*   When building the new request for a redirect, the redirect handler in tornado/simple_httpclient.py must create a fresh copy of the headers for the new request rather than sharing a reference to the original request's headers object, so that mutations to the new request's headers do not affect the original request.


*   Interface details: Type: Function
Name: _websocket_mask_python
Location: tornado/util.py
Signature: _websocket_mask_python(mask: bytes, data: bytes) -> bytes
Description: Pure-Python WebSocket masking function. Must raise ValueError("mask must be 4 bytes") if len(mask) != 4 before performing any masking.

Type: Function
Name: websocket_mask
Location: tornado/speedups.c
Signature: websocket_mask(mask: bytes, data: bytes) -> bytes
Description: C extension WebSocket masking function. Must raise ValueError("mask must be 4 bytes") if the mask argument is not exactly 4 bytes long.

Type: Class
Name: _GzipMessageDelegate
Location: tornado/http1connection.py
Description: Wraps an HTTPMessageDelegate to decode Content-Encoding: gzip. The constructor must accept a max_body_size: int parameter. During data_received, it must track cumulative decompressed byte count and raise httputil.HTTPInputError("decompressed body too large") if the total exceeds max_body_size.
Signature: __init__(self, delegate: httputil.HTTPMessageDelegate, chunk_size: int, max_body_size: int) -> None

Type: Function
Name: redirect handler (fetch_impl / _on_headers redirect branch)
Location: tornado/simple_httpclient.py
Description: When processing a 3xx redirect response in _HTTPConnection, the redirect logic must: (1) copy the original request headers into a new headers object for new_request rather than sharing a reference; (2) compare the scheme and netloc (including port) of the original request URL with the redirect target URL; (3) if they differ (cross-origin redirect), delete the Authorization and Cookie headers from new_request.headers and clear URL-embedded credentials; (4) if they are the same (same-origin redirect), preserve those headers. The header deletion loop must operate on new_request.headers, not self.request.headers.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.