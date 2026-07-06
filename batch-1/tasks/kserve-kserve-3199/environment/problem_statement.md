## Description

Inference graphs in KServe currently only support serverless deployment, which requires a specific set of serverless infrastructure components to be present in the cluster. This prevents teams who run plain Kubernetes clusters — without the serverless layer — from using inference graphs at all.

We need to add support for deploying inference graphs using standard Kubernetes resources. When a user opts into "raw" deployment mode via an annotation on the inference graph, the system should create a regular Kubernetes Deployment and Service instead of the serverless equivalents. The inference graph controller must detect this mode and route accordingly.

## Expected Behavior

- When an inference graph is annotated to use raw deployment mode, a Kubernetes Deployment and Service are created with the same name as the graph.
- No serverless service or routing resources should be created in raw deployment mode.
- The deployment must have one replica and contain a container configured with the router image and the serialized graph specification as arguments.
- Inference graph specs should support declaring minimum replicas, maximum replicas, a scaling metric (cpu, memory, concurrency, or rps), and a scaling target, enabling autoscaling configuration.
- A helper that builds the container pod specification for the graph must handle optional header propagation and custom resource requirements.
- A helper that constructs metadata for the deployment must merge the graph's existing labels and annotations and always include a standard inference-graph label.
- A function that propagates deployment readiness back onto the inference graph status must update the graph's ready condition and observed generation based on the underlying deployment state.

## Why This Matters

Many production Kubernetes environments do not run serverless infrastructure. Without raw deployment mode, those teams cannot use inference graphs at all. Adding this mode expands the deployment flexibility of KServe and aligns inference graph behavior with how inference services already support both raw and serverless modes.
