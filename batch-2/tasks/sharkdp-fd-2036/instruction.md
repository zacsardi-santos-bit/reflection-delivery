I've been using the file search tool and noticed an inconsistency in how it handles patterns that contain path separators.

*   When 'fd' is invoked with a primary pattern and a '--and' pattern that contains a forward slash (path separator), the command must exit with a failure status and output the error message: "[fd error]: The search pattern '<pattern>' contains a path-separation character and will not lead to any search results." where '<pattern>' is replaced with the exact '--and' pattern supplied by the user.

*   When '--full-path' is specified, the path-separator validation must be suppressed for '--and' patterns as well as for the primary pattern — the search must proceed normally and return matching results.

*   The path-separator diagnostic for '--and' patterns must use the same error message format as the existing diagnostic for the primary pattern, with the offending '--and' pattern interpolated into the message.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.