Implement health check endpoints for the OIDC Discovery Provider to enable liveness and readiness probes in containerized environments. Ensure these endpoints can be configured optionally and respond appropriately based on the provider's state.

*   Update the configuration:
    *   Add an optional `health_checks` section to the configuration.
        *   When present but empty, default values should be used:
            *   `BindPort`: 8008
            *   `ReadyPath`: '/ready'
            *   `LivePath`: '/live'
        *   Allow overriding of defaults when specific values are provided.
        *   If absent, the `HealthChecks` field in `Config` should be `nil`.

*   Modify the JWKSSource interface:
    *   Add a method `LastSuccessfulPoll() time.Time` in `support/oidc-discovery-provider/jwks_source.go`.
    *   Implement this method in both `ServerAPISource` and `WorkloadAPISource`:
        *   Return the timestamp of the last successful key fetch.
        *   Ensure thread safety using existing `RWMutex`.

*   Implement health check endpoints:
    *   Create a `NewHealthChecksHandler` function in `support/oidc-discovery-provider/healthchecks_handler.go`.
        *   Accept a `JWKSSource` and a `*Config`.
        *   Return a `HealthChecksHandler` that routes GET requests to `ReadyPath` and `LivePath`.
    *   Define `HealthChecksHandler` struct:
        *   Embed `http.Handler`.
        *   Implement `ServeHTTP` to handle requests.

*   Define readiness and liveness checks:
    *   Readiness endpoint:
        *   Return HTTP 200 if a successful poll occurred within the threshold window.
        *   Return HTTP 500 if no poll has succeeded or if the last poll is too old.
    *   Liveness endpoint:
        *   Return HTTP 200 if no poll has occurred yet (within startup grace period) or if the last poll was within the threshold.
        *   Return HTTP 500 if the last successful poll exceeded the threshold duration.

*   Calculate staleness threshold:
    *   Derive from the poll interval of the configured API source.
    *   Default to three minutes if the poll interval is zero or if five times the interval is less than three minutes.
    *   Consider a poll time five minutes in the past as stale.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.