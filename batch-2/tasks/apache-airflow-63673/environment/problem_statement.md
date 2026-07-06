# Pod Log Reading Fails on Non-UTF-8 Container Output

## Description

When reading pod logs asynchronously from Kubernetes, the log-fetching logic currently breaks when a container produces output containing bytes that are not valid UTF-8 characters. This situation arises in real workloads that emit binary data, use non-UTF-8 encodings, or produce truncated multi-byte sequences. The result is an unhandled encoding error that aborts the entire log read, leaving operators with no usable log output.

## Expected Behavior

- Asynchronous pod log reading should complete successfully even when the pod's output contains byte sequences that cannot be decoded as UTF-8.
- Invalid or undecodable bytes should be silently replaced with a substitution character rather than causing an exception.
- The returned logs should be a list of strings — one per line — containing all decodable content, with non-UTF-8 bytes represented by the standard Unicode replacement character.
- The same robust behavior should apply to both the base Kubernetes async hook and the Google Kubernetes Engine async hook.

## Why This Matters

Operators running tasks on Kubernetes often have no control over the encoding of the container's output. When the log reading fails mid-stream due to a single bad byte, the task loses visibility into what happened. Graceful handling of encoding errors ensures that log collection is always available, even for containers that mix binary and text output.
