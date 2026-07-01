I'm working on extending the kube-linter tool's health probe port validation. Right now, the tool checks that liveness probes reference ports that are actually exposed by the container — but only for HTTP and TCP socket probe types. There are two gaps I need to fill:

First, the newer gRPC-based health probe type isn't validated at all for liveness probes. If a container declares a gRPC health check against a port number that isn't in its exposed ports list, the linter doesn't catch it.

Second, readiness probes and startup probes aren't checked for port validity at all. We need new lint checks that cover these probe lifecycle types, with the same validation logic — flag any probe (HTTP, TCP socket, or gRPC) that references a port not exposed by the container. Containers with no probe configured, or with an exec-style probe, should be left alone.

The error messages for gRPC port mismatches should follow the same style as the existing HTTP and TCP socket messages, but reference the port by its integer number and clearly identify the probe as gRPC-based in the diagnostic.
