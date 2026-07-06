## Description

When using cloud or remote browsers, the WebSocket connection can appear healthy at the network layer (TCP keepalive stays alive) while the browser silently stops responding to commands. Without any timeout protection, the browser automation framework hangs indefinitely waiting for responses that will never come — individual browser protocol requests stall, action handlers never return, and the agent stops emitting steps entirely, producing empty history traces with no useful error information.

## Expected Behavior

- Individual browser protocol requests to a remote browser should time out after a configurable duration rather than hanging forever. When the timeout triggers, a descriptive error should be raised that identifies which command timed out and how long it was waited.
- The default timeout for browser protocol requests should be configurable via an environment variable, with a sensible built-in fallback. Invalid or malformed environment variable values (empty, non-numeric, zero, negative, infinity, not-a-number) should fall back silently to the default rather than crashing on startup.
- Each tool action execution should be guarded by a per-action timeout. When an action exceeds this timeout, the system should return a descriptive error result identifying the action that timed out, rather than hanging forever.
- The per-action timeout should also be configurable via an environment variable with the same defensive fallback behavior for invalid values.
- The default per-action timeout must be large enough that legitimate slow operations (such as content extraction which may take up to 120 seconds) are not prematurely killed.
- The browser session layer must use the timeout-protected browser protocol client when connecting to remote browsers.

## Why This Matters

Without these protections, a single unresponsive remote browser can cause the entire agent to stall indefinitely. Operators have no way to know the agent is stuck, and the agent never recovers on its own. With timeout guards at both the protocol request level and the action level, failures surface quickly with informative errors, and agents can continue to the next step rather than hanging.
