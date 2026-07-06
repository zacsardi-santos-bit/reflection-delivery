## Description

Hadoop's vectored read API allows applications to submit multiple non-contiguous file ranges for asynchronous parallel reading in a single call. However, the validation of requested ranges is currently inconsistent across filesystem implementations: different storage backends raise different exception types for the same invalid inputs (such as overlapping ranges), and invalid ranges that extend beyond the end of the file are only caught during the actual read rather than upfront.

This creates an unpredictable developer experience. Applications can't rely on consistent error types, and they may not learn about invalid requests until much later in an IO pipeline. The validation logic also needs to be usable by filesystem implementations that already know the file length at open time, so they can perform an early check without waiting for IO to fail.

## Expected Behavior

- Overlapping and duplicate ranges should always be rejected immediately with a consistent exception type, regardless of which filesystem backend is used.
- Filesystem implementations that know the file length at the time the vectored read is submitted should be able to validate ranges against that length upfront and reject out-of-bounds reads before any IO starts.
- A null range list or a list containing a null element should be rejected with a clear error.
- Reading the entire file as a single range should succeed. Reading more bytes than the file contains should be rejected immediately when the file length is known.
- Range validation utilities should return sorted ranges so callers get a consistent ordering.

## Why This Matters

Inconsistent validation behavior makes it hard to write portable code over the vectored IO API. Standardizing the error conditions and unifying the validation logic across all filesystem implementations reduces surprises and makes the API more reliable for downstream use.
