## Description

When debugging Python code in VS Code using the experimental debug adapter, there is currently no built-in way to log the communication between the IDE client and the debug adapter to a file. This makes it difficult to diagnose issues with debug sessions, since developers have no record of what messages were exchanged or when lifecycle events occurred.

## Expected Behavior

- A new logging component should be available that can optionally log all debug session traffic to a file when enabled by the session configuration.
- When file logging is enabled for a session, a dedicated log file should be created in the extension's root directory, named after the session identifier.
- The log file should capture all session lifecycle events — session start, session stop, errors, and exit codes and signals.
- Messages flowing from the adapter to the client and from the client to the adapter should both be logged, with clear indication of direction.
- When file logging is not enabled (or the configuration option is absent), no log file should be created and no writes should occur.
- The component that activates the debug adapter should register this new logging capability alongside the existing adapter descriptor factory, so that both are activated together when the experimental debug adapter mode is in use.

## Why This Matters

Developers debugging Python code in VS Code sometimes encounter mysterious debugger behavior that is hard to diagnose without visibility into the underlying debug protocol traffic. Providing an optional file-logging mechanism gives developers and contributors a way to capture this traffic and investigate issues more effectively.
