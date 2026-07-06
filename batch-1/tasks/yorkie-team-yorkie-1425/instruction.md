Implement a security check in the Admin API's document retrieval endpoint to ensure that a client can only access documents belonging to their authenticated project. If the project specified in the request does not match the project associated with the client's credentials, reject the request with an HTTP 401 Unauthorized response.

*   Update the document retrieval endpoint to include a validation step:
    *   Extract the project identifier from the Authorization header (the project's SecretKey).
    *   Compare this identifier with the 'project_name' field in the request body.
    *   If the identifiers do not match, return an HTTP 401 Unauthorized response.
*   Ensure that any request using a project's secret key in the Authorization header must specify the same project name in the request body to successfully retrieve documents.
*   Confirm that the endpoint does not return a 200 or any other success status when the project identifiers do not match.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.