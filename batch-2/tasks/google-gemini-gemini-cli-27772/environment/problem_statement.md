# Prompt Injection Protection for External Tool Results

## Description

The CLI uses tools to retrieve data from the outside world — fetching web pages, running shell commands, and calling external services. Any of those data sources could contain adversarially crafted text that mimics system instructions. Without protection, a malicious web page or external API response could instruct the model to ignore its original directives and take unintended actions.

## Expected Behavior

- All content returned by external tools (web fetches, shell commands, external service calls) should be enclosed in special marker tags before being included in the model's context. This creates a clear boundary between trusted system instructions and untrusted external data.
- The system prompt should include an explicit directive telling the model to ignore any commands or directives found inside those marker tags.
- The display-facing output shown to the user should remain unchanged — only the content sent to the model should be wrapped.
- The wrapping mechanism must be injection-safe: if the external data itself contains the closing marker tag, it must be escaped so the boundary cannot be breached from within.
- A dedicated utility function should handle the wrapping and escaping logic and be reused across all tool types.

## Why This Matters

Prompt injection attacks via external data are a well-known risk for AI systems that interact with the web or arbitrary external services. Establishing a clear, consistently applied trust boundary between system-controlled instructions and externally sourced data makes the system significantly more resilient to these attacks.
