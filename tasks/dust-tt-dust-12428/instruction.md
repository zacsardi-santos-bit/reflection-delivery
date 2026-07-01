Implement role-based permission checks for file management API endpoints to restrict write operations to users with appropriate access. Ensure that only users with builder or admin roles can modify or delete non-conversation files, while conversation files remain accessible to all authenticated users.

*   Update the public API handler located at `front/pages/api/v1/w/[wId]/files/[fileId].ts`:
    *   For POST requests:
        *   Return HTTP 403 with JSON error if the user is not a builder and the file use case is not 'conversation'.
        *   Return HTTP 400 with JSON error for non-system API keys attempting to modify non-conversation files.
    *   For DELETE requests:
        *   Return HTTP 403 with JSON error if the user is not a builder and the file use case is not 'conversation'.
        *   Return HTTP 400 with JSON error for non-system API keys attempting to delete non-conversation files.
    *   For GET requests:
        *   Return HTTP 404 with JSON error if the file does not exist.
        *   Return HTTP 302 and redirect to the signed download URL if the file exists.
    *   For unsupported HTTP methods (PUT, PATCH):
        *   Return HTTP 405 with JSON error indicating the method is not supported.

*   Update the private API handler located at `front/pages/api/w/[wId]/files/[fileId]/index.ts`:
    *   For POST requests:
        *   Return HTTP 403 with JSON error if the user is not a builder and the file use case is not 'conversation'.
    *   For DELETE requests:
        *   Return HTTP 403 with JSON error if the user is not a builder and the file use case is not 'conversation'.
    *   For GET requests:
        *   Return HTTP 404 with JSON error if the file does not exist.
        *   Return HTTP 302 and redirect to the signed download URL if the file exists.
    *   For unsupported HTTP methods (PUT, PATCH):
        *   Return HTTP 405 with JSON error indicating the method is not supported.

*   Ensure that conversation files bypass the builder permission check, allowing any authenticated user to perform POST or DELETE operations.
*   Allow users with builder or admin roles to perform POST or DELETE operations on any file, regardless of its use case.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.