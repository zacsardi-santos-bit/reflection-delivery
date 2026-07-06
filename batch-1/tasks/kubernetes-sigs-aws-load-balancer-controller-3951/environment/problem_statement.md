## Description

Several bugs and missing features have been identified in the AWS Load Balancer Controller related to OIDC secret handling, listener attribute conflict resolution, error message clarity, and observability.

## Issues

### OIDC Client Secret Trailing Characters

When OIDC authentication is configured and client credentials are stored in Kubernetes secrets, some tools or processes may add trailing whitespace or newline characters to the secret values. These extra characters are currently passed through verbatim into the load balancer configuration, causing authentication to fail silently even though the secret value looks correct when inspected. The controller should automatically strip trailing control characters from secret values before using them.

### Listener Attribute Conflict Resolution

When multiple ingresses in the same ingress group each set a listener attribute via annotation, and those ingresses produce conflicting values for the same attribute key, the controller returns an error. However, if an IngressClass parameter already defines the authoritative value for that attribute, the conflicting annotation values should be treated as resolved — the IngressClass configuration should win and no error should be raised.

### Ambiguous Error Message for Conflicting Load Balancer Attributes

When multiple ingresses define conflicting values for a load balancer attribute key, the error message does not indicate which type of resource has the conflict, making it harder to diagnose the problem. The error message should clarify that it is a load balancer attribute conflict.

### Missing Observability for Pod Readiness Gate Transitions

Currently there is no metric to track how long it takes for a pod's readiness gate to flip to healthy after being added to the load balancer. This makes it difficult to measure registration latency for pods using readiness gates. A metric should be emitted each time a readiness gate transitions from unhealthy to ready.

## Expected Behavior

- Trailing control characters in OIDC secret values are automatically stripped
- Listener attribute conflicts are ignored when the attribute is already defined by IngressClass parameters
- Conflicting load balancer attribute errors clearly identify the resource type
- A metric is recorded each time a pod readiness gate transitions to healthy, measuring the duration of the transition
