## Description

The jinja2 template expander service is currently configured to listen on port 50051 by default. This port is a common gRPC development/testing port, but it is not appropriate for the deployment context where the service is being used as a persistent gRPC service alongside other components. The service should instead listen on port 8443 by default.

## Expected Behavior

- When the jinja2 expander service starts without any explicit port arguments, it should bind to port 8443
- The service should still accept an explicit port argument to override the default
- All template validation and evaluation functionality should continue to work correctly on the new port

## Why This Matters

Port 8443 is the standard port used for secure service endpoints in Kubernetes deployments. Standardizing the expander service on this port aligns it with deployment conventions and ensures consistency with the surrounding infrastructure configuration. The existing tests connect to the service on port 8443, so they will fail if the server continues to start on port 50051.
