Implement support for dual-stack networking in the Kubernetes process monitor for FoundationDB by adding a new argument type that selects an IP address from a comma-separated list based on the specified IP family. Update error messages for consistency and provide a method to resolve argument values directly from environment variables.

*   Update error messages:
    *   Ensure error messages for missing environment variables start with lowercase: "missing environment variable {VAR_NAME}".

*   Add new argument type:
    *   Introduce `IPListArgumentType` as a constant in the `Argument` type enum located in `fdbkubernetesmonitor/api/config.go`.
    *   Add an `IPFamily` integer field to the `Argument` struct, accepting values 4 (IPv4) or 6 (IPv6), used when `ArgumentType` is `IPListArgumentType`.

*   Implement IP address selection:
    *   For `IPListArgumentType` with `IPFamily` 4, parse the IP list from the environment variable named by `Source` and return the first valid IPv4 address.
    *   For `IPListArgumentType` with `IPFamily` 6, parse the IP list from the environment variable named by `Source` and return the first valid IPv6 address.
    *   Silently skip invalid or unparseable entries in the IP list.

*   Handle errors:
    *   Return an error "could not find IP with family {N}" if no matching IP address is found for the requested `IPFamily`.
    *   Return an error "unsupported IP family {N}" if `IPFamily` is set to a value other than 4 or 6.

*   Implement `LookupEnv` method:
    *   Add `LookupEnv(env map[string]string) (string, error)` to the `Argument` struct in `fdbkubernetesmonitor/api/config.go`.
    *   For `EnvironmentArgumentType`, return `env[Source]` or an error "missing environment variable {Source}" if not present.
    *   For `IPListArgumentType`, apply the same IP-family selection logic as in `GenerateArgument`.

*   Update `GenerateArgument` method:
    *   Modify the existing `GenerateArgument(processNumber int, env map[string]string) (string, error)` method to support `IPListArgumentType` using the IP-family selection logic.
    *   Ensure lowercase error messages for missing environment variables.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.