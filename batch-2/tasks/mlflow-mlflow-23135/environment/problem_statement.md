## Description

Development scripts in the repository frequently need to look up version information for packages on the Python package index — for example, to find the latest stable release, check when a version was uploaded, or determine whether a release has been yanked or what Python versions it supports. Currently, there is no shared internal library for this purpose, so each script either reimplements the HTTP calls ad hoc or pulls in heavy external dependencies.

We need a small, self-contained internal library that development scripts can import to query the package index's JSON API in a clean, typed way.

## Expected Behavior

- Provide a synchronous function to fetch package metadata by name, returning a typed object that exposes the list of versions, the latest stable version, and per-release details (upload timestamp, yanked status, Python version compatibility).
- Results must be cached in memory so that looking up the same package more than once does not make redundant network calls.
- Provide an explicit way to clear the cache when fresh data is needed.
- Support pointing the client at a private mirror of the package index via an environment variable, in case the public index is not reachable.
- Retry automatically on transient network failures and rate-limit responses (up to 3 total attempts), but fail immediately on permanent errors like a missing package.
- Provide both synchronous and asynchronous entry points so the library is usable in both scripting and async contexts.
- Raise a dedicated error type for all unrecoverable fetch failures so callers can handle them uniformly.

## Why This Matters

Without a shared library, every development script that needs package version data has to handle HTTP errors, retries, caching, and JSON parsing on its own. A single, well-tested internal library removes that duplication and ensures consistent, reliable behavior across all scripts.
