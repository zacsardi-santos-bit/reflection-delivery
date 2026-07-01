## Description

webpack-dev-server currently uses a single hardcoded socket server implementation internally, with no way for users to configure or replace it. Teams with specialized WebSocket or socket handling requirements have no supported path to customize this behavior without forking the project.

## Expected Behavior

- A new configuration option should allow users to specify the socket server implementation the dev server uses.
- The option should accept the implementation as a short string identifier, a full file path to the implementation module, or the implementation class itself.
- A base class should be available for developers who want to write their own implementation, giving them a clear interface to follow.
- When the specified implementation cannot be found or is otherwise invalid, the server should fail with a clear, descriptive error rather than silently using a fallback or crashing unexpectedly.
- When no custom implementation is provided, the server should default to the existing built-in socket server behavior with no change in functionality.

## Why This Matters

Many real-world development environments have constraints or requirements around WebSocket connections that the default socket server does not accommodate. Allowing teams to plug in a custom socket server implementation — or select from multiple built-in ones by name — makes the dev server significantly more flexible without requiring users to maintain a fork.
