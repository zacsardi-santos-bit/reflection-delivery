## Description

The current S3 upload integration for prowler outputs relies on a set of standalone module-level functions. Each call requires passing the session, bucket name, output directory, filename, and output mode as separate arguments every time. There is no structured way to handle partial failures — if one upload fails, callers have no programmatic way to distinguish which files succeeded and which failed.

We should refactor the S3 upload logic into a class-based design that:
- Groups the session, bucket name, and output directory into a single reusable object
- Accepts a structured collection of output objects (organized by category) for batch uploading
- Returns a structured result clearly separating successful uploads (by file extension, with the resulting S3 keys) from failures (by file extension, with the object path and the originating error)

Additionally, the path-derivation helpers should be promoted to static methods on the new class so they can be called without instantiation.

## Expected Behavior

- A new class handles S3 uploads and is initialized with a session, bucket name, and output directory
- The upload method accepts a dict of categorized output objects (e.g., regular outputs and compliance outputs) and returns a result dict with two keys: one for successful uploads and one for failures
- Calling the upload method with no outputs returns empty success and failure dicts
- On success, each file extension maps to a list of S3 object keys that were uploaded
- On failure (e.g., the bucket doesn't exist), each file extension maps to a list of tuples containing the intended S3 key and the exception
- A static method derives the S3 key prefix from an output directory (stripping the path up to and including the "prowler/" segment when present)
- A static method maps common file extensions (such as CSV, HTML, and the two JSON format variants) to their corresponding S3 subfolder names

## Why This Matters

This refactoring makes the S3 integration easier to use and test, and allows callers to handle upload failures gracefully without crashing the entire scan output pipeline.
