## Description

The OpenAI instrumentation package correctly traces assistant runs when they are created manually and their status is checked separately. However, when developers use the higher-level convenience method that creates a run and automatically polls until it completes, no telemetry is generated. This is a significant gap because many real-world integrations prefer the simpler, one-call approach.

## Expected Behavior

- When an assistant run is started using the automatic polling approach (create and wait for completion), a single trace span must be produced with the same name and structure as the one produced by the manual approach.
- The span must record the type of operation, the model used (from the assistant definition), the system-level instructions from both the assistant and the run configuration, and all the messages produced during the run.
- The internal polling requests that check on run status must not produce additional spans — only one span for the entire operation.

## Why This Matters

Teams that rely on the simpler polling API to interact with AI assistants currently have a blind spot in their observability stack. Traces appear empty even though the assistant is completing work successfully. This inconsistency makes it difficult to audit, debug, or monitor AI assistant usage in production.
