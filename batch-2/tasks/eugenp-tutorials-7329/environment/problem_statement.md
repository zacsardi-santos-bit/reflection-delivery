## Description

When using a reactive HTTP client in Spring, developers frequently need to apply cross-cutting behaviors — such as logging outgoing requests, counting how many GET requests have been made, or versioning URLs by appending a path suffix — to every request without repeating that logic everywhere. Currently, there is no centralized utility class for these common filter patterns, so teams are forced to inline this logic each time they construct a new client instance.

## Expected Behavior

A utility class should exist that provides reusable filter factories for use when building reactive HTTP clients:

- A **URL-modifying filter** that accepts a version string and appends it as a path segment to every outgoing request URL.
- A **counting filter** that accepts a shared counter and increments it on each GET request, leaving it unchanged for other HTTP methods such as POST.
- A **logging filter** that accepts an output stream and writes a concise message identifying the HTTP method and target URL for every outgoing request, with no trailing newline.

## Why This Matters

These filter patterns are common in real applications — for versioned APIs, for monitoring request volumes, and for debugging outbound traffic. Having them in one place reduces boilerplate, makes behavior consistent, and makes it easy to compose multiple filters onto a single client.
