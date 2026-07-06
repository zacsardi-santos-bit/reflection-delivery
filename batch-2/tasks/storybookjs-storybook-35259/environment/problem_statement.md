## Description

When Storybook's dev server is launched by an AI coding assistant operating in a preview capacity, the server currently has no awareness of this context and ignores the AI environment's port assignment. This leads to port conflicts and makes the dev server inaccessible to the assistant's preview feature. Additionally, the server unnecessarily tries to open a browser window in environments where there is no user to interact with one.

## Expected Behavior

- The dev server startup process should be able to detect when it is running inside an AI assistant's preview environment.
- When running in that context, the port assigned by the AI environment should take priority over any other port configuration, since the assistant controls the networking.
- If no AI-assigned port is present in the environment, the explicitly configured port and then the secondary environment-based port configuration should be used in that order.
- When running in an AI preview context, the browser-open behavior should be automatically suppressed, even if the caller requested it.
- Outside of an AI preview context, existing port precedence and browser-open behavior should be unchanged.
- Port values from any source should be validated and rejected with a clear, descriptive error if they are not valid integers in the accepted range.

## Why This Matters

AI-powered coding assistants that auto-launch dev servers need the server to bind to a specific port they control. Without this awareness, the server either ignores the assigned port or conflicts with it. Clear port validation errors also help developers quickly identify misconfigurations rather than encountering cryptic runtime failures.
