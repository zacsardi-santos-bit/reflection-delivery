## Browser Agent Fails in Sandboxed Environments

### Description

When running the CLI inside a sandboxed environment — either the macOS filesystem sandbox or a Docker/Podman container — the browser automation feature fails in ways that are confusing and hard to debug.

In the macOS sandbox, using a persistent browser profile triggers filesystem permission errors because the sandbox policy restricts access to profile directories. The browser agent simply errors out without a clear explanation.

In Docker or Podman containers, Chrome is not installed inside the container, so any attempt to start the browser fails immediately. Even when the user enables the browser agent in their settings, it tries to launch Chrome and fails with no useful guidance.

### Expected Behavior

- When running under the macOS filesystem sandbox, the browser feature should automatically switch to an isolated, headless mode that works within sandbox restrictions — no user configuration required. An informative message should be shown explaining that isolated mode is being used for sandbox compatibility.

- When running inside a container sandbox, the browser agent should be disabled by default, and the user should receive a clear informational message explaining why and how to re-enable it by connecting to a Chrome instance running on the host machine.

- When the user configures existing session mode inside a container sandbox, the browser feature should automatically resolve the host machine's address and connect to Chrome on the host at port 9222, with an informational message showing the address being used.

- Outside any sandbox, existing browser behavior should be completely unchanged.

### Why This Matters

Users running the CLI in sandboxed or containerized environments get cryptic errors with no path forward. This change makes the browser feature work correctly across all supported sandbox types with sensible defaults and clear feedback.
