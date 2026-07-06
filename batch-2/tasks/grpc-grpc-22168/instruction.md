Fix the TLS client key material fetching logic to ensure it correctly distinguishes between client and server modes. Implement the necessary changes to allow TLS clients to succeed without pre-loaded key materials or a credential reload configuration, while maintaining the requirement for servers to have credentials. Additionally, ensure the TLS key materials configuration object correctly stores and retrieves set values.

Requirements:

*   Implement `grpc_tls_key_materials_config_create()`:
    *   Allocate and return a new `grpc_tls_key_materials_config` object.
    *   Location: `src/core/lib/security/credentials/tls/grpc_tls_credentials_options.cc`
    *   Signature: `grpc_tls_key_materials_config* grpc_tls_key_materials_config_create()`

*   Implement `grpc_tls_key_materials_config_set_key_materials()`:
    *   Accept a config pointer, a PEM root certificate string, an array of `grpc_ssl_pem_key_cert_pair` pointers, and a count.
    *   Store these values into the config object.
    *   Return 1 on success, 0 on failure.
    *   Location: `src/core/lib/security/credentials/tls/grpc_tls_credentials_options.cc`
    *   Signature: `int grpc_tls_key_materials_config_set_key_materials(grpc_tls_key_materials_config* config, const char* root_certs, const grpc_ssl_pem_key_cert_pair** key_cert_pairs, size_t num)`

*   Ensure `grpc_tls_key_materials_config` object accessors function correctly:
    *   `pem_root_certs()` must return the exact PEM root certificate string passed in.
    *   `pem_key_cert_pair_list()` must return a list of size 1, where the first element's `private_key()` returns the private key string and `cert_chain()` returns the certificate chain string.

*   Implement `TlsFetchKeyMaterials()`:
    *   Accept a `grpc_tls_key_materials_config` reference, a `grpc_tls_credentials_options` reference, a boolean `is_server` flag, and a `grpc_ssl_certificate_config_reload_status` pointer.
    *   When called in client mode (`is_server=false`) with no pre-loaded key materials and no credential reload config set, return `GRPC_STATUS_OK`.
    *   When called in server mode (`is_server=true`) under the same conditions, return `GRPC_STATUS_FAILED_PRECONDITION`.
    *   Location: `src/core/lib/security/security_connector/tls/tls_security_connector.cc`
    *   Signature: `grpc_status_code TlsFetchKeyMaterials(const grpc_core::RefCountedPtr<grpc_tls_key_materials_config>& key_materials_config, const grpc_tls_credentials_options& options, bool is_server, grpc_ssl_certificate_config_reload_status* status)`

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.