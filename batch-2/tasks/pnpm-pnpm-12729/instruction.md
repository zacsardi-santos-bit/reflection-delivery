I'm working on a package registry proxy and I'd like to improve how it serves tarballs from upstream sources.

*   The stream_verified_to_cache function must be synchronous (not async) and return Result<Body, TarballStreamError> immediately, where Body is an axum HTTP response body that streams bytes to the client as they arrive from the upstream.

*   If the upstream response declares a Content-Length exceeding max_bytes, stream_verified_to_cache must return Err(TarballStreamError::TooLarge { limit: max_bytes, received }) synchronously before any streaming begins, and the TarballWrite temp file must be cleaned up (removed) at that point.

*   When the streaming body returned by stream_verified_to_cache is dropped mid-stream (before end-of-stream), the associated TarballWrite must be dropped and its Drop implementation must remove the temporary cache file.

*   When an upstream serves bytes that fail SRI integrity verification, the proxy must respond with HTTP 200 OK and stream those bytes to the client; the tampered bytes must never be promoted to the cache (the temp file is abandoned).

*   When an upstream stream errors or truncates mid-transfer, the proxy must respond with HTTP 200 OK (status line already sent); draining the response body must surface as an error at the body level, and the incomplete bytes must never be promoted to the cache.

*   On a successful end-to-end transfer where the full body passes SRI verification, the proxy must respond with HTTP 200 OK, stream the complete bytes to the client, and promote the verified bytes to the cache file after the stream completes.


*   Interface details: Type: Function
Name: stream_verified_to_cache
Location: pnpr/crates/pnpr/src/streaming.rs
Signature: pub fn stream_verified_to_cache(response: reqwest::Response, write: TarballWrite, integrity: &Integrity, max_bytes: u64) -> Result<Body, TarballStreamError>
Description: Synchronous (non-async) function that streams an upstream HTTP response to the client while simultaneously teeing bytes into a cache temp file and computing the SRI hash. Returns a `Result<axum::body::Body, TarballStreamError>` immediately (before any bytes are transferred). The returned `Body` supports `.into_data_stream()` to yield chunks via `futures_util::StreamExt::next()`. If the upstream response declares a `Content-Length` exceeding `max_bytes`, the function returns `Err(TarballStreamError::TooLarge { limit: max_bytes, received })` synchronously before any streaming begins and the `TarballWrite` drop removes the tmp file. SRI verification happens at end-of-stream: on match the temp file is promoted to cache; on mismatch or any upstream error, the temp file is abandoned. If the body stream is dropped mid-way (client disconnect), the `TarballWrite` is dropped and its `Drop` impl removes the tmp file.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.