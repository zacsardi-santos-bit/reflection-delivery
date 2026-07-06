Implement SSH tunneling support in Gatus to enable routing health checks through SSH tunnels for services not directly accessible from the internet. Define named SSH tunnels in the configuration and allow endpoints to reference these tunnels. Validate configuration at startup to ensure all tunnel references are valid.

*   Create a new package `config/tunneling/sshtunnel`:
    *   Define a `Config` struct with exported fields: `Type`, `Host`, `Port`, `Username`, `Password`, and `PrivateKey`.
    *   Implement `Config.ValidateAndSetDefaults()` to:
        *   Return error "unsupported tunnel type: <type>" for unrecognized types.
        *   Return error "host is required" if `Host` is empty.
        *   Return error "username is required" if `Username` is empty.
        *   Return error "either private-key or password is required" if both `Password` and `PrivateKey` are empty.
        *   Set `Port` to 22 if it is 0.
    *   Provide a `New(config *Config) *SSHTunnel` constructor that:
        *   Returns a non-nil `SSHTunnel` with an unexported `config` field referencing the passed config.

*   Implement `SSHTunnel.Close()` to:
    *   Return nil even if no connection was established.
    *   Be safe to call multiple times without error.

*   Create a new package `config/tunneling`:
    *   Define a `Config` struct with:
        *   An exported `Tunnels` field of type `map[string]*sshtunnel.Config`.
        *   An unexported `connections` field for caching live tunnel instances.
    *   Implement `Config.ValidateAndSetDefaults()` to:
        *   Validate each tunnel configuration and wrap errors with "tunnel '<name>': ".
        *   Initialize the `connections` map after successful validation.
    *   Implement `Config.GetTunnel(name string)` to:
        *   Return error "tunnel '<name>' not found in configuration" for unknown names.
        *   Return the same `*sshtunnel.SSHTunnel` instance for known names, ensuring connection reuse.
    *   Implement `Config.Close()` to:
        *   Close all active tunnels in `connections`.
        *   Clear the `connections` map.

*   Update the `config` package:
    *   Add `validateTunnelingConfig(config *Config) error` to:
        *   Return nil if `config.Tunneling` is nil.
        *   Return specific errors for invalid tunnel references in endpoints and suite endpoints.
    *   Add `resolveTunnelForClientConfig(config *Config, clientConfig *client.Config) error` to:
        *   Return nil if `clientConfig.Tunnel` is empty.
        *   Return error if the named tunnel is not found.

*   Modify the main `Config` struct in the `config` package to include:
    *   A `Tunneling` field of type `*tunneling.Config`.

*   Update the `client.Config` struct to include:
    *   A `Tunnel` field of type `string` for specifying the SSH tunnel.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.