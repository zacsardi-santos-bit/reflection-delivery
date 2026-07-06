I'm hitting a silent data loss issue when reading files from Google Cloud Storage through ClickHouse's object storage table function.

*   The s3() table function must correctly return all data rows when the remote server omits the Content-Length header from both HEAD and GET HTTP responses (as occurs during GCS decompressive transcoding).

*   When a HEAD request to an object storage URL returns HTTP 200 OK without a Content-Length header, the skip-empty-files optimization must NOT treat the file as empty or skip it; the file must be read and its contents returned.

*   A SELECT count() query using s3() against a server that omits Content-Length must return the actual number of records in the response body (e.g., 3 rows for a body containing 3 newline-delimited JSON objects), not 0.

*   A SELECT * query using s3() with 'LineAsString' format against a server that omits Content-Length must return all records from the response body.

*   A new mock HTTP server file must exist at tests/integration/test_storage_s3/s3_mocks/gcs_transcode_mock.py. It must listen on a port passed as a command-line argument, respond to HEAD requests with 200 OK including ETag and Last-Modified headers but no Content-Length, and respond to GET requests with 200 OK returning the body '{"id":1}\n{"id":2}\n{"id":3}\n' with no Content-Length header.

*   The mock server must be registered in the integration test cluster under the service name 'resolver' on port 8084.


*   Interface details: Type: File
Name: gcs_transcode_mock.py
Location: tests/integration/test_storage_s3/s3_mocks/gcs_transcode_mock.py
Description: A standalone HTTP mock server that simulates GCS decompressive transcoding behavior by omitting the Content-Length header on both HEAD and GET responses. The server listens on a port specified as a command-line argument (sys.argv[1]). HEAD requests return HTTP 200 with Content-Type (application/octet-stream), ETag, Last-Modified, and Connection headers but no Content-Length. GET requests to any path other than "/" return HTTP 200 with the same headers plus a response body of {"id":1}\n{"id":2}\n{"id":3}\n and no Content-Length. GET requests to "/" return a plain "OK" health-check response. The server must be registered in the integration test cluster at host "resolver" port 8084 alongside the other mock servers.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.