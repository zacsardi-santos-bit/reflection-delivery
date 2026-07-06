Implement a configurable startup timeout for the router's connection to a controller. Add a new optional setting in the router's controller configuration section to specify this timeout duration, allowing operators to adjust the wait time during startup.

*   Update the `Config.Ctrl` struct in `router/config.go`:
    *   Add a `StartupTimeout` field of type `time.Duration`.
    *   Ensure `StartupTimeout` is added in the following order with existing fields:
        *   `InitialEndpoints []*UpdatableAddress`
        *   `LocalBinding string`
        *   `DefaultRequestTimeout time.Duration`
        *   `Options *channel.Options`
        *   `DataDir string`
        *   `Heartbeats env.HeartbeatOptions`
        *   `StartupTimeout time.Duration`
*   Ensure the `StartupTimeout` field allows operators to specify the duration the router waits during startup before exiting if it cannot connect to a controller.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.