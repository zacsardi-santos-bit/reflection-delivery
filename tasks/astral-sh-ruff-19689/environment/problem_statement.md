# Workspace Diagnostic Caching Broken for Paths with Special Characters

## Description

The workspace diagnostic caching mechanism doesn't work correctly when file paths contain special characters such as colons. When a client sends back the previous result identifiers with their URIs encoded differently than what the server originally returned (for example, because the client applies additional percent-encoding to special characters such as colons in the path), the server fails to match those identifiers to the cached results. Instead of recognizing the files as unchanged and entering a lightweight long-polling state, the server treats all files as if they were brand new and performs unnecessary full re-analysis.

## Expected Behavior

- When a client sends previous result IDs whose file URI encoding differs from what the server originally returned (for example, because the client applies additional percent-encoding to special characters in the path), the server should still recognize these as matching the same files.
- The server should compare file identifiers based on the underlying file path, not the raw URI string.
- When all files are matched and unchanged, the server should respond with an empty diagnostic report rather than sending full reports for every file.

## Why This Matters

This affects workspaces where paths contain characters that get encoded differently by different clients. Workspace paths containing colons (common in some URI conventions and Windows paths) trigger this bug, causing the server to skip all caching on every subsequent request. This results in unnecessary recomputation and breaks the efficiency of the pull-diagnostics caching protocol.
