## Description

The MLflow S3 artifact repository fails to correctly detect an S3 bucket's AWS region when the bucket-existence check returns a successful (non-error) response. This is a problem in environments where the caller has limited bucket permissions — in these cases, the check returns a normal response containing region information in the HTTP response headers rather than raising an error. The current implementation only extracts the region when an error is thrown, so in the successful-response case the S3 client ends up configured without the correct region.

## Expected Behavior

- When the initial S3 bucket check returns a successful response and the region is present in the response's HTTP headers, that region should be extracted and used to configure the S3 client.
- The existing behavior (extracting region from a thrown error's response headers) should remain unchanged.
- All other S3 client parameters (TLS verification, endpoint URL, credentials) should continue to work correctly regardless of whether the bucket check succeeded or raised an error.

## Why This Matters

In many real-world AWS setups — particularly those with fine-grained IAM policies that restrict permissions — the bucket-existence check does not raise an error even when the client is configured without the bucket's region. Without the fix, users experience S3 connectivity or authentication failures that are hard to diagnose, because the client silently operates with the wrong region configuration.
