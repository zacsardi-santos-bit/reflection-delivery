## Description

The storage package is missing two utilities that other parts of the system need: a way to compute a sensible piece size from a file's total content length, and a way to check whether a task file already exists on disk given the URL and associated metadata.

Without a standard piece-length calculator, each caller invents its own sizing logic, leading to inconsistencies. And without a task-stat utility, callers cannot efficiently check for a pre-existing task file without knowing the internal directory layout and ID-generation rules.

## Expected Behavior

- A piece-length calculator should accept a content length and return a piece size that keeps the total piece count at or below 500. The resulting size must be rounded up to the nearest power of two, then clamped between a minimum of 4 MB and a maximum of 64 MB. A content length of zero should return the minimum piece size (4 MB).
- A task-stat function should accept a storage base path, a URL, and optional metadata (content length, piece length, tag, application name, filtered query parameters). It should locate the task file on disk and return its filesystem metadata.
- The task-stat function must return an error when the base path or URL is empty.
- The task-stat function must return an error when neither a content length nor a piece length is supplied.
- When only a content length is given, the task-stat function derives the piece length automatically using the piece-length calculator.
- The task-stat function should use a functional-options pattern so that optional fields (tag, application, piece length, content length, filtered query parameters) can be set independently.

## Why This Matters

These utilities give callers a single, consistent way to determine piece sizes and to query task storage existence without duplicating ID-generation and directory-layout logic throughout the codebase.
