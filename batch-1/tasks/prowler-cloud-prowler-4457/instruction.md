Refactor the S3 upload logic in the prowler tool by implementing a class-based design. Create a new class to manage S3 uploads, encapsulating session, bucket name, and output directory. Ensure the class can handle batch uploads and return structured results for successes and failures.

*   Implement the `S3` class in `prowler/providers/aws/lib/s3/s3.py`.
    *   Initialize with `session: boto3.Session`, `bucket_name: str`, and `output_directory: str`.
*   Implement the `send_to_bucket` method.
    *   Accept a `dict` (keyword: `outputs`) mapping category strings to lists of output objects.
    *   Return a `dict` with keys 'success' and 'failure'.
    *   On empty input, return `{'success': {}, 'failure': {}}`.
    *   On success, map file extensions to lists of S3 object keys.
    *   On failure, map file extensions to lists of tuples `(object_key: str, exception: Exception)`.
    *   Ensure the exception message includes 'An error occurred (NoSuchBucket) when calling the PutObject operation: The specified bucket does not exist' for non-existent buckets.
*   Construct S3 object keys using `{get_object_path(output_directory)}/{subfolder}/{basename_of_file}`.
    *   Use `output.file_descriptor.name` for the basename.
    *   For 'regular' category, determine subfolder using `generate_subfolder_name_by_extension(output.file_extension)`.
    *   For 'compliance' category, use 'compliance' as subfolder.
*   Store all files with ContentType 'binary/octet-stream'.
*   Implement `get_object_path` as a static method.
    *   Return the path portion after 'prowler/' if present, otherwise return the full path.
*   Implement `generate_subfolder_name_by_extension` as a static method.
    *   Return 'csv' for '.csv', 'html' for '.html', 'json-asff' for '.asff.json', and 'json-ocsf' for '.ocsf.json'.
*   Handle missing `file_descriptor` attributes by creating temporary files and assigning them to `output.file_descriptor`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.