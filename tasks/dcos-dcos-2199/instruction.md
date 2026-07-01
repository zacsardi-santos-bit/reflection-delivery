Implement separate TLS configuration files for Master and Agent Admin Routers in DC/OS, ensuring distinct security settings for each. Allow independent toggling of TLS protocol versions for the Master Admin Router and enforce a strict TLS 1.2 policy for the Agent Admin Router. Prevent configurations that disable all TLS versions simultaneously and support custom cipher suite overrides for the Master Admin Router.

*   Split TLS configuration into two files:
    *   `/etc/adminrouter-tls-master.conf` for Master Admin Router.
    *   `/etc/adminrouter-tls-agent.conf` for Agent Admin Router.
*   Master Admin Router configuration:
    *   Include a configurable `ssl_ciphers` directive, defaulting to `EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5`.
    *   Allow custom cipher suite override via `adminrouter_tls_cipher_suite`.
    *   Dynamically set `ssl_protocols` based on enabled TLS versions using `adminrouter_tls_version_override`.
    *   Ensure the configuration file structure includes specific blank lines and comments.
*   Agent Admin Router configuration:
    *   Fixed content with `ssl_ciphers EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:!MD5;`, `ssl_prefer_server_ciphers on;`, and `ssl_protocols TLSv1.2;`.
*   Register and validate configuration variables:
    *   `adminrouter_tls_1_1_enabled` and `adminrouter_tls_1_2_enabled` as boolean strings, defaulting to 'true'.
    *   `adminrouter_tls_cipher_suite` as a string, defaulting to ''.
    *   Ensure validation errors for invalid boolean values.
*   Implement calculated variables:
    *   `adminrouter_tls_cipher_override` to determine cipher suite usage.
    *   `adminrouter_tls_version_override` to construct the `ssl_protocols` string.
*   Validate TLS version configuration:
    *   Ensure at least one of `adminrouter_tls_1_0_enabled`, `adminrouter_tls_1_1_enabled`, or `adminrouter_tls_1_2_enabled` is 'true'.
    *   Raise an error if all are 'false' with a specific message.
*   Update configuration templates in `gen/dcos-config.yaml`:
    *   Use `adminrouter_tls_cipher_override` and `adminrouter_tls_version_override` for conditional configurations.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.