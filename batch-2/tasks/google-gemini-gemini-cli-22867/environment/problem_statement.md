## Description

The browser automation sub-agent currently has no safety controls around sensitive operations. When the agent performs actions like filling forms, executing scripts, or uploading files, it does so silently without offering the user any chance to review or block these actions. This creates a security and trust gap, especially in automated workflows that may interact with sensitive web forms or handle file data.

Additionally, the permission engine cannot properly match tool permission checks that use short (unqualified) tool names against permission rules that were registered using the full qualified naming convention. This means browser sub-agent tools that are registered with fully qualified names may not be correctly evaluated during permission checks.

## Expected Behavior

- A configuration option should allow users to require manual confirmation before the browser agent performs sensitive actions such as form filling, script evaluation, and file uploading.
- A separate configuration option should allow users to completely block any file upload attempt by the browser agent. When blocked, the agent should receive a clear error message rather than the upload proceeding silently.
- Read-only browser operations (such as taking screenshots or listing open pages) should be automatically permitted without requiring user confirmation, reducing unnecessary prompts.
- The permission system should correctly resolve permission checks that use short tool names (without the server prefix) against rules defined with the fully qualified name, so that sub-agent tools are properly governed regardless of how the tool name is presented during the check.

## Why This Matters

Without these controls, users running browser automation have no way to enforce confirmation on sensitive data entry actions or prevent unauthorized file uploads. The naming mismatch in the permission system also means that any rules registered for browser sub-agent tools with qualified names may be silently bypassed when the check uses the short name form.
