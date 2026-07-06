Implement the `MetadataFromFileName` function to infer HTTP metadata from a filename, ensuring that uploaded artifacts are served with the correct content type and encoding. This function will modify the filename if necessary and return a metadata map with appropriate headers.

Requirements:

*   Implement `MetadataFromFileName` in `prow/pod-utils/gcs/metadata.go` with the signature:
    *   `MetadataFromFileName(filename string) (string, map[string]string)`
    *   Returns a potentially modified filename and a map of HTTP metadata headers.

*   Handle files with standard extensions:
    *   For extensions like '.txt' or '.json', return the filename unchanged.
    *   Include 'Content-Type' in the metadata map, using the MIME type for the extension (e.g., 'text/plain; charset=utf-8' for '.txt', 'application/json' for '.json').

*   Handle compressed files with recognized inner extensions:
    *   For files like '.txt.gz' or '.json.gz', strip the compression suffix.
    *   Return metadata with 'Content-Encoding' set to 'gzip' and 'Content-Type' set to the MIME type of the inner extension.

*   Handle compressed files without recognized inner extensions:
    *   For files like 'build-log.gz', strip the compression suffix.
    *   Return metadata with 'Content-Type' set to 'application/gzip' (no 'Content-Encoding').

*   Handle filenames that are compression suffixes:
    *   For filenames like 'gz', return the filename unchanged.
    *   Include 'Content-Type' set to 'application/gzip' in the metadata.

*   Handle special cases:
    *   For an empty filename, return an empty string and an empty (non-nil) map.
    *   For a filename consisting of only a single dot ('.'), return '.' and an empty (non-nil) map.

*   Utilize the standard MIME type registry:
    *   Ensure that runtime-registered extensions (e.g., '.log' mapped to 'text/plain') are recognized and produce the correct 'Content-Type'.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.