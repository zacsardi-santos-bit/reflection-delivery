I'm working on the OIDC Discovery Provider component and I need to add health check endpoints so it can be deployed in containerized environments with liveness and readiness probes.

Right now there's no way for the orchestration platform to know whether the provider has successfully loaded key material or is still alive. I want to add an optional configuration section that, when present, starts a dedicated HTTP listener on localhost exposing two endpoints: one for readiness and one for liveness.

The readiness check should only return success when the provider has successfully fetched key material and that fetch was recent enough — if no keys have ever been loaded, or if the last successful fetch was too long ago, it should return a failure status. The liveness check should be a bit more lenient: right after startup, before any key fetch has completed, the service should still be considered live. Once key fetching has been established, the liveness check should fail if polling has gone stale past a threshold.

When the health checks section is present but empty in the configuration, the endpoints should default to sensible values for the port and URL paths. Operators should be able to override those defaults. If the section is absent entirely, no health check listener should be started.

Both endpoints should return HTTP 200 for a healthy state and HTTP 500 for an unhealthy state. The staleness threshold should be derived from the configured poll interval, with a minimum floor to avoid being too aggressive.

The key source interface also needs to expose the time of the last successful poll so the health check handler can determine freshness, and both the server API and workload API source implementations need to track and expose that timestamp.
