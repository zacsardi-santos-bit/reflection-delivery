## Description

There are two bugs in the Bruno-to-Postman collection exporter that cause incorrect output when importing Bruno collections into Postman.

**Bug 1: File paths in multipart form data are wrapped in arrays**

When exporting a Bruno collection that contains multipart form data with file attachments, the file path for each file field is incorrectly wrapped in an array. Postman expects a plain string for a single file path, not an array. As a result, importing the exported collection into Postman produces malformed form data.

Additionally, when a file field has no path set, the exporter currently emits an empty array instead of an absent value, which also violates Postman's expected format.

**Bug 2: GET and HEAD requests with bodies lose their body on Postman import**

Postman automatically strips (prunes) the body from HTTP methods that are conventionally bodyless, such as GET and HEAD. Postman provides a special flag to override this behavior and preserve the body for these methods. The Bruno-to-Postman exporter does not currently set this flag, so any GET or HEAD requests that have a body in Bruno will silently have their body discarded when imported into Postman.

## Expected Behavior

- File fields in multipart form data should export their path as a plain string, not an array.
- File fields with no path should export their path as an absent value, not an empty array.
- GET and HEAD requests that include a body should include a flag in the exported Postman item that tells Postman to preserve (not prune) the body.
- POST and other method requests, as well as GET/HEAD requests with no body, should not include this flag.

## Why This Matters

Users who rely on Bruno-to-Postman export to share or migrate collections will find that file uploads and GET/HEAD requests with bodies are broken or missing in Postman after importing, leading to incorrect test or API behavior.
