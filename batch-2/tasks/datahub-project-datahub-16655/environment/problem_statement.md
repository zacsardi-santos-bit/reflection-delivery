## Description

When DataHub's metadata ingestion tools make API calls to the server, there is currently no way to know what tool or environment originated those calls. Whether the ingestion was triggered by a CI pipeline, an AI coding assistant, a terminal session, or another automation tool, all requests look the same to the server. This makes it difficult to audit, trace, or attribute API activity by its source.

## Expected Behavior

- The ingestion client should automatically detect the calling context at runtime using a tiered strategy: first by checking for an explicit caller override, then by examining known environment variable signals, then by inspecting the parent process's environment, and finally by walking the process tree.
- The detected caller label should be included in the HTTP User-Agent header of every outgoing request.
- Users should be able to override the auto-detected caller by setting a dedicated environment variable.
- The detection should recognize common environments such as CI systems, AI coding assistants, and terminal sessions.
- The detection logic must be robust: it should never crash or raise an exception. If detection fails for any reason, it should fall back gracefully to a safe default value.
- The result should always be a compact, header-safe string.

## Why This Matters

This gives the DataHub server (and operators) visibility into what tools are generating API traffic — enabling better observability, debugging, and support for multi-tool environments without requiring any changes from tool authors.
