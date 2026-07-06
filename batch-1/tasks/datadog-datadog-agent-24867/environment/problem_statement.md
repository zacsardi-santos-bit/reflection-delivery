## Description

The current pipeline tooling uses a custom wrapper class to communicate with the GitLab API via raw HTTP requests. This approach requires manually implementing retry logic for server-side errors, manually parsing JSON responses, and representing GitLab resources (like pipeline jobs) as plain Python dictionaries. As a result, job attributes are accessed with string keys spread across multiple files, and the code must handle low-level HTTP concerns such as 5xx retry loops.

There is an official GitLab Python client library available that handles all of this properly: it manages retries, provides structured response objects, and gives attribute-based access to GitLab resources. We should migrate the pipeline notification and statistics tooling to use this library.

## Expected Behavior

- A new module should be created in the CI provider utilities that wraps the official library and provides a factory function for obtaining a configured API client.
- The existing functions for reading GitLab CI configuration files and resolving includes should be available from this new module.
- The pipeline notification functions that list jobs and fetch job logs should use the official library's API client instead of raw HTTP calls.
- Job objects throughout the notification and statistics code should be the library's job type rather than plain dictionaries, with all attribute access updated accordingly (including the job URL field, which uses a different attribute name in the library's objects than the key previously used in dictionaries).
- The old custom wrapper class and its HTTP retry logic can be removed.

## Why This Matters

Using the official library reduces maintenance burden (no custom retry logic), improves type safety (structured objects instead of dicts), and ensures forward compatibility with the GitLab API as the library handles versioning and serialization details.
