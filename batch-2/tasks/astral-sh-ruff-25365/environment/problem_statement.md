## Description

The language server does not handle certain edge cases gracefully. When a client sends a hover request or a document diagnostic request for a file that is not currently open in the server, the server fails to return a proper empty response. Additionally, when a code action resolve request arrives with missing or unparseable data (i.e., the data field is absent or contains something that cannot be interpreted as a document reference), the server crashes or panics rather than returning the original action unchanged.

## Expected Behavior

- A hover request for a file that is not open should return an empty (null) response, not hang or error.
- A document diagnostic request for a file that is not open should return a full diagnostic report with an empty list of items.
- A code action resolve request with no data payload should return the original code action unchanged.
- A code action resolve request with a data payload that is not a valid document reference should return the original code action unchanged.

## Why This Matters

LSP clients may send requests in any order, and there are legitimate scenarios where a request arrives for a file the server does not have open. Similarly, not all code actions come from Ruff — a client may ask the server to resolve a third-party code action that lacks the expected data format. The server should handle these cases gracefully instead of failing, ensuring robustness and compatibility with a wider range of LSP clients.
