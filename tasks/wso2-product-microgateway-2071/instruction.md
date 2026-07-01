Implement default timeout settings for upstream routes in the API gateway to ensure requests to slow backends are terminated appropriately. Configure the gateway to apply a maximum request duration of 60 seconds and an idle connection timeout of 300 seconds by default, with these values being adjustable through the configuration file.

*   Update the `createRoute` function in `adapter/internal/oasparser/envoyconf/routes_with_clusters.go`:
    *   Set the `Timeout` field on the `RouteAction` of every generated upstream route to 60 seconds.
    *   Set the `IdleTimeout` field on the `RouteAction` of every generated upstream route to 300 seconds.
    *   Use `ptypes.DurationProto` to convert these timeout values from configuration to protobuf Duration values.
    *   Ensure the function signature is `createRoute(params *routeCreateParams) *routev3.Route`.

*   Modify the configuration structs in `adapter/config/types.go`:
    *   Create a struct `upstreamTimeout` with fields:
        *   `RouteTimeoutInSeconds` of type `time.Duration` (TOML key: `routeTimeoutInSeconds`).
        *   `RouteIdleTimeoutInSeconds` of type `time.Duration` (TOML key: `routeIdleTimeoutInSeconds`).
    *   Extend the existing `envoyUpstream` struct to include a `Timeouts` field of type `upstreamTimeout`.

*   Set default configuration values in `adapter/config/default_config.go`:
    *   Ensure `Envoy.Upstream.Timeouts.RouteTimeoutInSeconds` is set to 60.
    *   Ensure `Envoy.Upstream.Timeouts.RouteIdleTimeoutInSeconds` is set to 300.
    *   These defaults should be used by unit tests to verify that the route action has `Timeout = 60s` and `IdleTimeout = 300s`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.