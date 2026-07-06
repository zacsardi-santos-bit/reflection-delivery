## Description

The minio-go library needs support for AWS S3 Express directory buckets. S3 Express uses a distinct bucket naming convention (with a specific availability zone suffix) that distinguishes it from standard S3 buckets. Without a utility function to detect this naming convention, the library cannot make routing or behavioral decisions based on whether a bucket is an S3 Express directory bucket.

Additionally, the library would benefit from two new generic, type-safe internal utilities: a deduplication mechanism for concurrent identical operations, and a reusable key-value cache — both to provide type safety and eliminate the need for internal ad-hoc implementations.

## Expected Behavior

- A utility function should be available to detect whether a given bucket name conforms to the S3 Express directory bucket naming format.
  - Valid S3 Express bucket names end with a specific two-part suffix that includes an availability zone identifier followed by a fixed marker. The AZ ID portion follows a known format: a short alphanumeric region code, followed by "-az" and a single digit (e.g. usw2-az1, use1-az5, apne1-az4).
  - Bucket names that are IP addresses, start with a dot, contain consecutive dots or consecutive hyphens in the name portion, or have an invalid AZ segment should not be recognized as S3 Express buckets.
- A new generic key-value cache package should be available, usable directly from its zero value without any initialization.
- A new generic singleflight package should be available that deduplicates concurrent calls for the same key, propagates panics correctly to all callers, handles goroutine exits gracefully, and supports both synchronous and asynchronous call styles.

## Why This Matters

As S3 Express directory buckets require different handling from standard buckets, client libraries need a reliable way to identify them. The generic cache and singleflight utilities modernize internal code and make these patterns reusable across the library with type safety.
