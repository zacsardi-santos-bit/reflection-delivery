## Description

We need a new utility that can record Retrieval-Augmented Generation (RAG) trace data to a local log file for debugging purposes. Currently, there is no way to capture details about which code snippets are retrieved during RAG operations — such as their content and relevance scores — in a way that persists across sessions.

## Expected Behavior

- A new logging component should be initializable with a directory path. On initialization, it must create that directory if it doesn't exist, setting strict directory permissions (owner-only access) to protect potentially sensitive data.
- Once initialized, the logger should accept structured RAG trace entries containing a session identifier, a status string, and a list of retrieved snippets, and append each entry as a single JSON line to a named log file inside the initialized directory. Each entry must include an automatically generated timestamp.
- On the first write, the log file's permissions must also be enforced to owner-only access — even if the file was pre-existing.
- If the logger is used before being initialized, it should report a warning through the debug logging system rather than failing.
- If directory creation or file writing fails, the error should be reported through the debug logging system with a descriptive message rather than propagating the exception.

## Why This Matters

This feature enables developers and operators to debug RAG retrieval behavior by inspecting what snippets are being retrieved and their relevance scores in a persistent local trace file. Without this, RAG-related issues are difficult to investigate post-hoc.
