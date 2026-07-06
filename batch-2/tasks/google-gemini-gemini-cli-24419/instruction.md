Implement changes to the browser automation feature to handle sandboxed environments effectively. Ensure the feature operates seamlessly in macOS filesystem sandboxes and Docker/Podman containers, providing clear feedback to users.

*   Update the `BrowserManager` class in `packages/core/src/agents/browser/browserManager.ts`:
    *   Implement sandbox detection in `ensureConnection()` using the `SANDBOX` environment variable.
        *   Detect a container sandbox when `SANDBOX` is set and not equal to 'sandbox-exec' or 'sandbox:none'.
        *   Detect a seatbelt sandbox when `SANDBOX` is 'sandbox-exec'.
    *   Modify MCP transport arguments based on the detected environment:
        *   In a seatbelt sandbox with non-existing session mode:
            *   Include `--isolated` and `--headless` in arguments.
            *   Exclude `--userDataDir` and `--autoConnect`.
            *   Emit an info-level message: 'isolated browser session'.
        *   In a seatbelt sandbox with existing session mode:
            *   Include `--autoConnect`.
            *   Exclude `--isolated` and do not force `--headless`.
        *   In a container sandbox with existing session mode:
            *   Resolve 'host.docker.internal' using DNS lookup.
            *   Include `--browser-url` and `http://<resolved-ip>:9222`.
            *   Exclude `--autoConnect`.
            *   Emit an info-level message with the resolved IP and port.
        *   Outside any sandbox with persistent session mode:
            *   Include `--userDataDir`.
            *   Exclude `--isolated` and `--autoConnect`.

*   Update the `AgentRegistry` class in `packages/core/src/agents/registry.ts`:
    *   Modify `initialize()` to check for container sandbox environments before registering the browser agent.
        *   If in a container sandbox and session mode is not 'existing':
            *   Do not register the browser agent.
            *   Emit an info-level message: 'Browser agent disabled in container sandbox'.
        *   Register the browser agent normally if in a container sandbox with existing session mode, in a seatbelt sandbox, or outside any sandbox.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.