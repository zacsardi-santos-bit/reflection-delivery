Extend the kube-linter tool to validate readiness and startup probes, ensuring they reference ports exposed by the container. Implement checks for gRPC-based health probes across all probe lifecycle types, and update existing liveness probe validation to include gRPC checks.

*   Create a new template package for readiness probe validation:
    *   Location: `pkg/templates/readinessport/`
    *   Register with the template system using a `templateKey` constant.
    *   Include an internal `params` subpackage at `pkg/templates/readinessport/internal/params/` with an empty `Params` struct and necessary functions: `ParamDescs`, `ParseAndValidate`, `WrapInstantiateFunc`.
    *   Return no diagnostics if the readiness probe is nil or uses an exec action.
    *   For gRPC readiness probes, return no diagnostics if the port matches an exposed TCP port. Otherwise, return a diagnostic: `container "NAME" does not expose port PORT for the GRPC check`.

*   Create a new template package for startup probe validation:
    *   Location: `pkg/templates/startupport/`
    *   Follow the same structure as the readinessport package.
    *   Return no diagnostics if the startup probe is nil or uses an exec action.
    *   For gRPC startup probes, return no diagnostics if the port matches an exposed TCP port. Otherwise, return a diagnostic: `container "NAME" does not expose port PORT for the GRPC check`.

*   Extend the existing liveness probe validation template:
    *   Include validation for gRPC probes.
    *   Return no diagnostics if the gRPC liveness probe port matches an exposed TCP port. Otherwise, return a diagnostic: `container "NAME" does not expose port PORT for the GRPC check`.

*   Implement a utility function `CheckProbePort`:
    *   Location: `pkg/templates/util/check_probe_port.go` or inline in each template.
    *   Signature: `CheckProbePort(container *v1.Container, probe *v1.Probe) []diagnostic.Diagnostic`
    *   Return nil for nil probes or exec actions.
    *   For gRPC probes, compare the integer port number against exposed TCP container ports and return a diagnostic if unmatched.
    *   Exclude non-TCP ports from matching.

*   Ensure all probe types (HTTP, TCP socket, gRPC) are validated for port exposure.
*   Maintain consistent error message formatting for diagnostics, clearly identifying the probe type and port number.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.