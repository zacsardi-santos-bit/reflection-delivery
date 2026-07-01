Implement support for shared secrets in the secret management system, allowing secrets to be referenced by any service without duplication. Update the CLI tool to handle shared secrets and ensure the runtime can decrypt both service-specific and shared secrets.

*   Update secret reference logic:
    *   Modify `is_secret_ref` in `paasta_tools/secret_tools.py` to recognize `SHARED_SECRET(<name>)` as valid.
    *   Implement `is_shared_secret` in `paasta_tools/secret_tools.py` to return `True` if a string starts with 'SHARED_', `False` otherwise.
    *   Modify `get_secret_name_from_ref` to extract names from `SHARED_SECRET(<name>)`.

*   Manage shared secrets in CLI:
    *   Add a new constant `SHARED_SECRET_SERVICE` in `paasta_tools/secret_tools.py` with the value '_shared'.
    *   Modify `paasta_secret` in `paasta_tools/cli/cmds/secret.py` to:
        *   Use `SHARED_SECRET_SERVICE` when `args.shared` is `True` and `args.clusters` is provided.
        *   Call `sys.exit(1)` if `args.shared` is `True` and `args.clusters` is `None` or empty.
    *   Update `print_paasta_helper` to accept a boolean `is_shared` parameter.

*   Handle shared secrets in runtime:
    *   Create `decrypt_secret_environment_for_service` in `paasta_tools/cli/cmds/local_run.py` with the specified signature.
    *   Modify `decrypt_secret_environment_variables` to:
        *   Separate environment variables into service-specific and shared secrets using `is_shared_secret`.
        *   Call `decrypt_secret_environment_for_service` for both service-specific and shared secrets.
        *   Return a merged dictionary of decrypted secrets.
        *   Exit with `sys.exit(1)` on any decryption failure.

*   Update secret retrieval:
    *   Modify `get_hmac_for_secret` to use `SHARED_SECRET_SERVICE` for shared secrets, constructing file paths as `<soa_dir>/<SHARED_SECRET_SERVICE>/secrets/<secret_name>.json`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.