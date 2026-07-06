## Description

When trying to read data into pandas using memory-mapped mode with an in-memory buffer object, users receive a cryptic, low-level error about a missing file descriptor. This error originates deep in the operating system and provides no helpful context about why the operation failed or what the user should do instead.

## Expected Behavior

- When memory-mapped reading is requested with an in-memory buffer, pandas should raise a clear, descriptive error that tells the user memory mapping is only supported when reading from a real file path.
- The error type should be a standard value error, not an obscure internal exception from the operating system layer.

## Current Behavior

- A low-level internal error about a missing file descriptor is surfaced directly to the user with no explanation of the actual constraint.

## Why This Matters

Users who pass in-memory buffers while also requesting memory-mapped reading have no way to understand from the current error what went wrong or how to fix it. A clear, actionable error message immediately communicates the limitation — memory mapping requires an actual file on disk — and helps users correct their code without having to dig through documentation or source code.
