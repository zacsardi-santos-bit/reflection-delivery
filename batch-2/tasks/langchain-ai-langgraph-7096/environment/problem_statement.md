## Description

The LangGraph CLI currently only supports a single combined deployment model where all components of the server stack — API, job queuing, and execution — run within a single container. This limits horizontal scalability because you cannot independently scale the job execution layer without also scaling the API layer.

We need to add support for a **distributed runtime mode** that splits the stack into three dedicated services:
- An **API service** that handles HTTP requests
- An **orchestrator service** that routes work to executors
- An **executor service** that processes jobs using a dedicated container image

## Expected Behavior

- The CLI's Dockerfile generation command should accept a runtime mode flag, allowing users to select either the existing combined mode or the new distributed mode.
- In distributed mode, the generated Dockerfile should target the executor base image rather than the API base image.
- An explicit custom base image flag should still override the distributed mode default.
- When generating multi-service Docker Compose configurations in distributed mode, the output should include separate services for the orchestrator and executor with the correct inter-service communication settings.
- The API service in distributed mode should be configured to delegate job execution to the executor, not process jobs itself.
- Environment files specified in the project config should be propagated to all services (API, orchestrator, and executor) in distributed mode.
- The default combined mode should continue to work exactly as before, with no orchestrator or executor services added.

## Why This Matters

Teams that need to scale job execution independently from the API layer — for example, to handle high-throughput workloads — cannot do so with the current single-container model. Supporting distributed deployments via the CLI makes it straightforward to adopt this architecture without manually writing complex multi-service configurations.
