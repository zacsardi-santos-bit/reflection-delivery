Refactor the ECS agent's introspection server into a structured, testable package with a well-defined API. Implement programmatic server construction with configurable options such as timeouts and profiling endpoints. Introduce a typed error system to map backend failures to correct HTTP status codes and operational metrics.

*   Implement typed error structs:
    *   Define `ErrorMultipleTasksFound`, `ErrorNotFound`, and `ErrorFetchFailure` in `ecs-agent/introspection/v1/`.
    *   Provide constructors: `NewErrorMultipleTasksFound(reason string)`, `NewErrorNotFound(reason string)`, and `NewErrorFetchFailure(reason string)`.
    *   Ensure each error implements `Error() string` and `MetricName() string`.
    *   Enable unwrapping via `errors.As()`.

*   Develop HTTP handlers:
    *   `AgentMetadataHandler` and `TasksMetadataHandler` in `ecs-agent/introspection/v1/handlers/` must handle requests and return appropriate HTTP status codes and metrics.
    *   Implement `licenseHandler` to return license text or handle errors.

*   Create server construction utilities:
    *   Implement `NewServer` in `ecs-agent/introspection/` to construct an `http.Server`.
    *   Validate `agentState` and `metricsFactory` are non-nil, returning specific error messages if they are.
    *   Default `ReadTimeout` and `WriteTimeout` to 0; allow configuration via `WithReadTimeout` and `WithWriteTimeout`.
    *   Use `WithRuntimeStats` to enable profiling endpoints and adjust `WriteTimeout`.

*   Configure server routing:
    *   Ensure pprof paths are only active when profiling is enabled.
    *   Define root endpoint to list available commands based on profiling status.

*   Implement utility functions:
    *   `WriteStringToResponse` in `ecs-agent/tmds/handlers/utils/` must write responses with "text/plain" content type.
    *   `logFriendlyContentType` must convert MIME types to human-readable labels.

*   Handle errors and metrics:
    *   Map errors to HTTP status codes using `getHTTPErrorCode`.
    *   Emit appropriate metrics for each error type.
    *   Recover from handler panics, returning 500 status and emitting a crash metric.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.