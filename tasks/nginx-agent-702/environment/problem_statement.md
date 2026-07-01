# Add Config Upload Response Flow and Connection-Gated File Operations

## Description

Currently, when the management plane sends a configuration upload request to the agent, the agent processes the request but never sends any response back to the management plane. This leaves the management plane unaware of whether the upload succeeded or failed.

Additionally, the file management component does not know when a connection to the management plane has been established. As a result, file operations such as uploading file overviews or updating individual files can be attempted before a connection is ready, leading to failures.

## Expected Behavior

- After the agent processes a configuration upload request from the management plane, it should send a status response back (indicating success or failure) so the management plane can confirm the request was handled.
- The command component should accept and forward these data plane response messages through the agent's internal messaging bus.
- The file management component should only perform file operations after a successful connection to the management plane has been established. A new connection notification should be used to signal that file operations can proceed.
- Configuration upload requests forwarded through the internal messaging bus should carry the full management plane request structure, not just the inner upload request payload.
- The config parser should respect the list of allowed directories when processing configuration files.

## Why This Matters

Without a response mechanism, the management plane is left in the dark about whether configuration uploads were completed successfully. Without connection-gating, file operations may fail silently during startup before the connection is fully established. These changes create a more reliable and complete control loop between the agent and the management plane.
