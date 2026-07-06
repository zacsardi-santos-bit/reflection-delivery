Refactor the Determined AI agent's configuration system to improve accessibility, serialization, and merging of configuration options. Export GPU visibility helpers, update the options struct, and implement a proper configuration merging function with correct precedence.

*   Export GPU visibility helpers:
    *   Move and export `CudaVisibleDevices` and `RocrVisibleDevices` constants to `agent/internal/options/options.go`.
    *   Export `VisibleGPUsFromEnvironment` function in `agent/internal/options/options.go`. This function should check `RocrVisibleDevices` first, then `CudaVisibleDevices`, and return an empty string if neither is set.

*   Implement default configuration retrieval:
    *   Create `DefaultOptions` function in `agent/internal/options/options.go` returning a pointer to `Options` with specified default values such as `Log.Level="trace"` and `BindPort=9090`.

*   Update the Options struct:
    *   Ensure the `Options` struct in `agent/internal/options/options.go` uses JSON tags for YAML field mapping.
    *   Rename TLS fields to `TLSCertFile` and `TLSKeyFile` with JSON tags `tls_cert` and `tls_key`.
    *   Add a `Log` field of type `logger.Config` with JSON tag `log`.
    *   Support strict YAML unmarshaling for all documented fields.

*   Implement configuration merging:
    *   Define a module-level `v *viper.Viper` variable in `agent/cmd/determined-agent`.
    *   Create a constant `viperKeyDelimiter` with value ".." in `agent/cmd/determined-agent`.
    *   Implement `mergeConfigIntoViper(bs []byte) (*options.Options, error)` in `agent/cmd/determined-agent` to merge YAML config bytes into `v`, respecting precedence: flags > environment variables > config file > defaults.
    *   Ensure `mergeConfigIntoViper` returns a zero-value `Options` struct when `bs` is empty, and handles errors appropriately.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.