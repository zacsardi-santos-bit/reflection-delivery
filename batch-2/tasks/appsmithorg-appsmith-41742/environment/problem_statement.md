## Description

When users upload binary files (such as PDFs) through the REST API connector and bind the file data to a query or template, the file content is being silently corrupted. Binary files often contain byte sequences that resemble HTML special character notations, and the template rendering system was incorrectly converting all such sequences to their decoded equivalents. This alters the actual binary data before it is sent in the request, making the uploaded file unreadable or invalid on the receiving end.

## Expected Behavior

- When a binding value is inserted into a template, HTML entity sequences such as those representing less-than, greater-than, ampersand, line feed, carriage return, and similar patterns must pass through unchanged.
- Only the HTML encoding for double-quote characters should be converted (to escaped double quotes), as this is required for JSON validity.
- Text that appears directly in the template (outside of binding expressions) must not be altered in any way — if the template itself contains an HTML entity, it must appear unchanged in the output.
- When a multipart file upload payload is encoded as a JSON array and the value has leading whitespace or newlines, it must still be correctly recognized and processed as a structured file array rather than being misrouted to a different handler.
- Binary file data containing HTML entity-like sequences must survive the multipart upload pipeline without corruption.

## Why This Matters

Users are experiencing silent data corruption when uploading binary files (PDFs, images, etc.) via dynamic REST API requests. The corrupted data causes the uploaded file to be unreadable or rejected by the target server. This issue affects any file whose binary content happens to contain byte patterns that resemble HTML entity sequences, which is common in standard binary file formats.
