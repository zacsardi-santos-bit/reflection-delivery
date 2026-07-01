## Description

The snippet generation library supports many programming languages and HTTP client libraries, but currently has no support for Dart. Developers working in Dart have no way to generate ready-to-use HTTP client code snippets from the library.

## Expected Behavior

- A new Dart HTTP snippet generator should be available in the library
- Given a request description (URL, method, headers, cookies, query parameters, body, and credentials), it should produce a complete, runnable Dart source file
- It should handle the full range of common HTTP request patterns:
  - GET and POST requests (and other methods)
  - Request headers, with proper handling of empty values and duplicate names
  - Query string parameters with correct URL encoding of special characters
  - Cookies with proper URL encoding
  - JSON request bodies
  - URL-encoded form data
  - Multipart form data
  - Binary/raw body content
  - Basic authentication credentials (only when both username and password are present)
- Edge cases like empty collections, special characters in URLs, and extremely long URLs should be handled gracefully

## Why This Matters

Dart is a popular language (notably for Flutter development), and developers using this library to generate API documentation or interactive clients currently cannot provide Dart code examples. Adding this support closes that gap and makes the library more useful for teams working across multiple languages.
