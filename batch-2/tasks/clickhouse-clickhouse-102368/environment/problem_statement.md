## Description

When querying files stored in Google Cloud Storage that were uploaded with compression, GCS can perform "decompressive transcoding" — transparently decompressing the object before delivering it to the client. Because the final uncompressed size is not known ahead of time, GCS omits the file size from its HTTP response headers entirely.

ClickHouse's object storage reader currently treats a missing size header as a file size of zero. This interacts badly with the "skip empty files" optimization, which silently skips the file and returns zero rows without any error or warning.

## Expected Behavior

- When ClickHouse queries a file via the object storage table function and the server's response does not include a size header, ClickHouse should read and return all available data from the response body.
- The empty-file optimization should only apply when the server explicitly reports a size of zero — not when the size is simply absent.

## Why This Matters

Users querying GCS-hosted compressed files through ClickHouse get silently empty result sets. There is no error, no warning — just missing data. This makes the bug particularly hard to diagnose. The fix should ensure that all records are returned when the remote server does not provide a size hint.
