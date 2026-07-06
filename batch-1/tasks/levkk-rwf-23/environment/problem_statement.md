## Description

The web framework currently supports reading form submissions encoded as URL-encoded data, but it cannot parse form submissions that use multipart encoding. This is a significant gap because browsers use multipart encoding for forms that include file inputs, and it is also the format used when developers explicitly set the encoding type on a form.

When a client sends a request with a multipart content type and a boundary marker, calling the method to retrieve form data from the request fails or returns nothing, making it impossible to read any of the submitted fields.

## Expected Behavior

- When a request arrives with a multipart content type and a boundary parameter, the framework should be able to parse the body and make form fields accessible.
- Named text fields from multipart form submissions should be retrievable as typed values, just like URL-encoded form fields are today.
- The framework should correctly identify field boundaries within the multipart body using the boundary string from the content type header.

## Why This Matters

Supporting multipart form data is essential for handling real-world form submissions, especially those involving file uploads or mixed content. Without it, developers cannot process standard browser form submissions that include file inputs.
