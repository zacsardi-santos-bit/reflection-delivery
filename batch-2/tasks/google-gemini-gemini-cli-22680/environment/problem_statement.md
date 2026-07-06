## Security Vulnerability: Environment Variable Redaction Can Be Bypassed

### Description

There is a security vulnerability in the sandbox system where callers can explicitly disable environment variable redaction when executing sandboxed commands. This means that sensitive credentials, API keys, tokens, and other secrets present in the environment could be passed directly to sandboxed processes, defeating the purpose of sandboxing.

Even when redaction is left enabled, variables explicitly added to the "allowed" list bypass sensitive-pattern filtering. A caller could allowlist a token-like variable name and have it pass through even though it matches known dangerous patterns.

### Expected Behavior

- Environment variable redaction must always be enforced, regardless of what any caller requests. Attempting to disable it should be silently overridden.
- Variables that match known sensitive name patterns or appear on the never-allowed list should be redacted even if they are explicitly listed as allowed.
- Safe variables (those not matching any sensitive pattern) should still be passable through the allowed list.
- A centralized utility should handle this "secure merge" logic so all sandbox implementations share the same security guarantees.

### Additional Feature: Linux OS-Level Sandboxing

The tool currently lacks a proper OS-level isolation backend for Linux. A new sandbox backend for Linux should use OS-level filesystem and process isolation, binding only the workspace and explicitly allowed paths as writable, while the rest of the filesystem is read-only. This backend should be selected automatically when running on Linux with sandboxing enabled.

The factory function that creates sandbox managers should be updated to accept the workspace path as a parameter so it can be passed to the new Linux backend. On non-Linux platforms with sandboxing enabled, the existing fallback manager should continue to be used.

### Why This Matters

Leaking credentials or tokens into sandboxed subprocesses is a serious security risk. Enforcing redaction unconditionally prevents accidental or malicious bypasses. The new Linux sandboxing backend provides true process isolation, reducing the blast radius of any compromised sandboxed command.
