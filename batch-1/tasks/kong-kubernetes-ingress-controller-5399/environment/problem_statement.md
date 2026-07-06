## Description

The controller currently has two related gaps around protocol annotation handling:

1. **Missing WebSocket protocol support**: WebSocket protocols are valid Kong protocol values but are rejected as unrecognized by the controller's protocol validation logic. Users who configure services or routes to use WebSocket connections receive validation errors even though the configuration is legitimate.

2. **No upfront annotation validation on admission**: The admission webhook does not check whether protocol annotations on Ingress or HTTPRoute resources contain valid values at submission time. Users can successfully deploy a misconfigured resource and only discover the problem later — if at all — which makes debugging significantly harder.

## Expected Behavior

- WebSocket protocols (secure and non-secure) should be accepted as valid protocol values by the controller's protocol validation logic.
- An empty protocol annotation value should also be treated as valid (meaning "use the default").
- When an Ingress or HTTPRoute resource is submitted with an invalid protocol annotation, the admission webhook should immediately reject it with a descriptive error message indicating which annotation value is invalid.
- When overrides are applied and a service carries an invalid protocol annotation, a translation failure should be recorded so that operators can identify the problem.

## Why This Matters

Without upfront validation, operators have no way to catch protocol misconfiguration at deploy time. They must instead debug runtime behavior after the fact. Adding protocol annotation validation at admission gives immediate, actionable feedback and prevents misconfigured resources from entering the cluster.
