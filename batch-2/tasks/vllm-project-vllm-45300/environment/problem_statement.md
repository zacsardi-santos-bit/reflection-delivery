## Description

The vllm Rust CLI's `serve` command accepts several configuration flags intended to control the behavior of the underlying Python engine process — including a shutdown timeout and a flag to disable statistics logging. However, these flags are currently parsed by the CLI frontend but never forwarded to the engine subprocess. As a result, users who configure these options find them silently ignored.

This is particularly impactful for the shutdown timeout: without forwarding it to the engine, in-flight requests are abruptly terminated when the server shuts down, regardless of what the user specified. Similarly, disabling statistics logging has no effect on the engine's actual logging behavior.

## Expected Behavior

- When a user starts the server with a shutdown timeout configured, that timeout value should be passed through to the Python engine so graceful shutdown works as expected.
- When a user starts the server with statistics logging disabled, that flag should be forwarded to the engine so the engine respects it.

## Why This Matters

Users rely on these settings for production deployments — graceful shutdown prevents dropping requests mid-flight, and suppressing log stats reduces noise in logging pipelines. Currently both settings are accepted by the CLI without error, giving users a false sense that their configuration is applied.
