## Description

Node.js applications deployed on the Azure serverless platform are currently not instrumented by the New Relic agent. Teams running their business logic as serverless functions on Azure get no automatic transaction tracing, no distributed tracing support, no performance metrics, and no cloud-specific attributes in their telemetry. This is a significant gap because serverless deployments on Azure are increasingly common, and developers rely on observability data to diagnose latency, cold starts, and downstream service calls.

## Expected Behavior

- When a Node.js application uses the Azure Functions SDK to register HTTP handlers, the agent should automatically instrument those registrations without requiring manual code changes.
- Each HTTP invocation should be recorded as a web transaction with a name reflecting the function name.
- The transaction should carry function-as-a-service attributes: invocation ID, function name, trigger type (derived from the trigger configuration), and a fully-qualified Azure cloud resource identifier built from the deployment's environment variables.
- The first invocation after a cold start should be marked as a cold start; subsequent invocations should not carry that marker.
- Incoming distributed tracing headers should be accepted and linked to the parent trace.
- Queue time derived from request headers should be tracked on the transaction.
- The port from the request URL should be captured.
- If the required environment variables are not present or properly formatted at startup, the agent should log a structured warning indicating which variables were expected and what values were found, rather than crashing or silently failing.
- The agent's logging infrastructure should support embedding structured data under a named key in log entries so diagnostic details can be attached to log messages.

## Why This Matters

Without this instrumentation, Azure Functions users cannot monitor their serverless workloads through New Relic. Adding automatic support for the Azure Functions SDK lets teams get full observability — transactions, spans, metrics, and distributed traces — out of the box, matching what is already available for other serverless platforms.
