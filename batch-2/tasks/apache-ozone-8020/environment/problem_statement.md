## Description

The S3 gateway needs two related improvements to keep the codebase maintainable and accurate.

First, a string constant representing the S3 content hash header is currently defined in a signing-specific class, but it is useful across multiple parts of the gateway. It should be moved to the shared constants file so other components can import it from a single, well-known location.

Second, the gateway's internal data stream copying logic currently uses a simple buffer copy utility. This should be replaced with a variant that explicitly accepts an offset and a byte length limit, which is better suited for large objects and gives more precise control over how much data is transferred. All places where object data is written — plain PUT, server-side object copy, and multipart part upload — should use this approach.

## Expected Behavior

- The content hash header constant is importable from the shared S3 constants class.
- Object data is copied using the offset-and-length form of the stream copy utility.
- If an error occurs mid-transfer (e.g., the client disconnects), the checksum computation state is properly reset regardless of whether the interrupted operation was a PUT, a copy, or a multipart part upload.

## Why This Matters

These changes align the implementation with cleaner utility patterns for large-file handling and ensure the existing error-recovery tests compile and run correctly against the actual production code.
