Implement two improvements in the Ozone S3 gateway to enhance code maintainability and accuracy. Move a string constant for the S3 content hash header to a shared constants file, and update the data stream copying logic to use a more precise utility method that includes offset and length parameters.

*   Define the constant X_AMZ_CONTENT_SHA256 in the shared constants file:
    *   Location: `org.apache.hadoop.ozone.s3.util.S3Consts`
    *   Type: `public static final String`
    *   Value: `"x-amz-content-sha256"`
    *   Ensure it is importable as `org.apache.hadoop.ozone.s3.util.S3Consts.X_AMZ_CONTENT_SHA256`.

*   Update the data stream copying logic in the S3 gateway object endpoint:
    *   Use `IOUtils.copyLarge(InputStream, OutputStream, long inputOffset, long length, byte[] buffer)` from Apache Commons IO.
    *   Apply this method in the following operations:
        *   PUT object operations:
            *   Use offset `0` and the expected content length.
        *   Server-side object copy operations:
            *   Use offset `0` and the source key length.
        *   Multipart part upload operations:
            *   Use offset `0` and the expected part length.

*   Ensure error handling during data transfer:
    *   If `IOUtils.copyLarge` throws an `IOException` during PUT object, copy object, or multipart part upload operations, reset the message digest instance used for ETag computation.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.