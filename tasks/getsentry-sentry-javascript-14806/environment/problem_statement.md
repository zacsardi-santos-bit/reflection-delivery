## Description

The Sentry JavaScript SDK core package needs a reorganization of its request data utilities. Several utility functions related to cookie parsing and IP address extraction are currently buried inside an internal utility module that was not designed to be a canonical home for these helpers. These should be moved to cleaner, more logical locations within the package.

In addition, the SDK is missing a dedicated module of utilities for extracting structured request data from HTTP requests. Currently there is no consistent way to get a normalized representation (including headers, URL, query string, cookies, and body data) from both traditional Node.js HTTP requests and modern edge-runtime (WinterCG-compatible) requests.

## Expected Behavior

- Cookie parsing and IP address extraction utilities should be accessible from cleaner, non-internal module locations.
- A new request utility module should provide functions to:
  - Convert headers from both WinterCG-style and plain-object formats into simple string dictionaries, filtering out any multi-value (array) headers.
  - Extract structured request data from WinterCG-compatible requests, including headers, method, URL, and query string.
  - Extract structured request data from Node.js HTTP-style requests, including automatic URL reconstruction when only a relative path and host header are available, protocol detection (including from TLS socket state), query string extraction, cookies, and request body.
  - Extract query parameters from any URL string, returning undefined if no query string is present.

## Why This Matters

As the SDK needs to support multiple server environments — traditional Node.js servers, edge runtimes, and frameworks with non-standard request shapes — having well-organized, consistent utilities for normalizing request data ensures error reports always include complete and accurate request context.
