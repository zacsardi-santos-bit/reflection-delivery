Implement conditional rate limiting based on JWT token claims in the NGINX Kubernetes Ingress Controller. Ensure rate limit policies can specify conditions that apply only when a JWT claim matches a specified value. Validate that JWT conditions are only used in the commercial version and handle configuration deduplication.

*   Update Structs:
    *   Modify `AuthJWTClaimSet` in `internal/configs/version2/` to include fields:
        *   `Variable string`
        *   `Claim string`
    *   Add a field `AuthJWTClaimSets []AuthJWTClaimSet` to `VirtualServerConfig`.
    *   Create `JWTCondition` in `pkg/apis/configuration/v1/` with fields:
        *   `Claim string`
        *   `Match string`
    *   Create `RateLimitCondition` in `pkg/apis/configuration/v1/` with fields:
        *   `JWT *JWTCondition`
        *   `Default bool`
    *   Add a field `Condition *RateLimitCondition` to `RateLimit` in `pkg/apis/configuration/v1/`.

*   Implement Functions:
    *   `generateAuthJwtClaimSetVariable(claim string, vsNamespace string, vsName string) string` in `internal/configs/virtualserver.go`:
        *   Return a variable name in the format `"$jwt_{vsNamespace}_{vsName}_{claim}"` with dots in `claim` replaced by underscores.
    *   `generateAuthJwtClaimSetClaim(claim string) string` in `internal/configs/virtualserver.go`:
        *   Return the `claim` with dots replaced by spaces.
    *   `removeDuplicateAuthJWTClaimSets(ajcs []version2.AuthJWTClaimSet) []version2.AuthJWTClaimSet` in `internal/configs/virtualserver.go`:
        *   Return a deduplicated slice of `AuthJWTClaimSet` based on the `Variable` field.

*   Modify `GenerateVirtualServerConfig`:
    *   Populate `AuthJWTClaimSets` in `VirtualServerConfig` with unique JWT claims from rate limit policies.
    *   Use `generateAuthJwtClaimSetVariable` and `generateAuthJwtClaimSetClaim` for constructing entries.
    *   Deduplicate entries using `removeDuplicateAuthJWTClaimSets`.

*   Update Validation:
    *   In `validateRateLimit`, return an error if a JWT-based condition is specified and `isPlus` is false.
    *   Return an error if `isPlus` is true and a condition is declared without a `JWTCondition`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.