## Description

The inference graph router container is deployed without a readiness probe, which means Kubernetes has no reliable way to determine when the router is actually ready to serve traffic. As a result, requests can be routed to a pod before it has fully initialized, and traffic continues to reach pods that are in the process of shutting down.

## Expected Behavior

- The inference graph router container should have a readiness probe configured so Kubernetes can determine when the pod is ready to serve traffic.
- The same readiness probe should be applied consistently in both raw Kubernetes deployments and Knative-based deployments.
- When the router is shutting down, it should be able to signal its non-ready state so that traffic can be drained before the process terminates.

## Why This Matters

Without a readiness probe, there is no safe way for the platform to manage the lifecycle of inference graph router pods. New pods may receive traffic too early, and terminating pods may drop in-flight requests. Adding a proper readiness probe ensures graceful startup and graceful shutdown behavior, improving reliability for users of the inference graph feature.
