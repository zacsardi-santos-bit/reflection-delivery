Implement the following improvements in the runtime codebase to enhance encapsulation and security:

*   Update the Universal struct in `pkg/api/universal/universal.go`:
    *   Change the field names to lowercase (unexported) identifiers:
        *   `logger` (previously `Logger`)
        *   `resiliency` (previously `Resiliency`)
        *   `compStore` (previously `CompStore`)
    *   Ensure the struct is initialized in tests using `&Universal{logger: ..., resiliency: ..., compStore: ...}` and accessed directly within the same package.

*   Modify the IsEnvVarAllowed function in `pkg/runtime/processor/secret/secret.go`:
    *   Implement case-insensitive filtering for environment variable keys.
    *   Deny keys matching the "DAPR_" prefix regardless of case (e.g., both 'dapr_foo' and 'DAPR_FOO' should be denied).
    *   Deny the "APP_API_TOKEN" key regardless of case (e.g., both 'app_api_token' and 'APP_API_TOKEN' should be denied).
    *   Ensure allowlist entries are matched in a case-insensitive manner (e.g., if 'FOO' is in the allowlist, then 'foo' should be allowed).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.