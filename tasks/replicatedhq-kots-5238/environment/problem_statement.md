## Description

When an application is downloaded from its vendor endpoint and the response body is malformed or corrupted compressed data, the system should provide clear and actionable error messages. Currently, when the server returns data that cannot even be opened as compressed content, the error reporting is inadequate — it doesn't distinguish this case from a successfully opened but truncated stream, and it fails to surface any human-readable text that may be embedded in the response body.

## Expected Behavior

- When the downloaded data is completely invalid as compressed content (i.e., decompression cannot begin at all), the error should clearly indicate that the compressed reader could not be created.
- When a response body contains a mix of binary and readable text (such as a vendor error message embedded in or alongside binary data), the system should be able to extract the readable portions and include them in error output.
- A utility that tracks the most recently seen bytes from a data stream should be available so that response data can be inspected after a failure even if the stream was not fully buffered.
- A utility that extracts readable text segments from arbitrary byte sequences should be available, skipping very short fragments (fewer than 5 characters) and joining multiple text segments with a clear separator.

## Why This Matters

When something goes wrong during an application update download, operators need actionable error output to diagnose the problem. If the vendor server returns an unexpected or malformed response, the system should make every effort to surface any readable information from that response rather than failing silently or with a generic error.
