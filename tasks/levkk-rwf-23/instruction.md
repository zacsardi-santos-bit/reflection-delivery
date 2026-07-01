Extend the existing form data handling in the Rust web framework to support parsing multipart form data from incoming HTTP requests. Ensure that the framework can correctly parse and retrieve form fields from requests with a multipart content type and boundary parameter.

*   Update the `form_data` method in `rwf/src/http/request.rs`:
    *   Implement parsing for requests with a Content-Type of 'multipart/form-data' and a boundary parameter.
    *   Ensure `form_data(&self) -> Option<FormData>` returns a populated `FormData` object instead of an error or `None`.

*   Modify the `FormData` enum in `rwf/src/http/form_data.rs`:
    *   Add a new variant to support multipart form data alongside the existing `UrlEncoded` variant.
    *   Ensure the `get<T: FromStr>(&self, name: &str) -> Option<T>` method retrieves named field values from both URL-encoded and multipart form data.

*   Implement the `from_request` function in `rwf/src/http/form_data.rs`:
    *   Detect if the request has a 'multipart/form-data' content type.
    *   Extract the boundary parameter from the Content-Type header.
    *   Parse the request body using the boundary to delimit parts, handling the standard RFC 2046 multipart format:
        *   Boundaries delimited by '--<boundary>'.
        *   Each part must start with a 'Content-Disposition: form-data; name="<fieldname>"' header, followed by a blank line and the field value.
        *   The body ends with a closing '--<boundary>--' terminator.
    *   Return `Err` if the content type is unsupported or the boundary is missing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.