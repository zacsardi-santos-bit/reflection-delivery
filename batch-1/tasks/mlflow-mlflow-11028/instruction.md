Implement a method to correctly detect and use the AWS region for an S3 bucket in environments with limited permissions. Ensure the S3 client is configured with the correct region whether the bucket check succeeds or raises an error.

*   Update the `_get_s3_client` method in `mlflow/store/artifact/optimized_s3_artifact_repo.py` within the `OptimizedS3ArtifactRepository` class.
    *   Ensure the method signature is `_get_s3_client(self) -> boto3 S3 client`.
    *   Use a temporary S3 client to call `head_bucket` to check the bucket's existence.
*   When `head_bucket` returns a successful response:
    *   Extract the AWS region from the response's HTTP headers using the key `x-amz-bucket-region` found under `ResponseMetadata -> HTTPHeaders`.
    *   Use this region to configure the final S3 client.
*   When `head_bucket` raises a `ClientError`:
    *   Continue extracting the region from the error response's HTTP headers using `error.response['ResponseMetadata']['HTTPHeaders']['x-amz-bucket-region']`.
    *   Use this region to configure the final S3 client.
*   Ensure the final S3 client is created with the `region_name` parameter set to the extracted region.
*   Maintain all other S3 client configuration parameters (TLS verification, endpoint URL, credentials) unchanged and correctly passed to the client constructor, regardless of the success or error of the `head_bucket` response.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.