Implement support for specifying response headers to be exposed in cross-origin requests within the API gateway's CORS configuration. Extend the internal CORS configuration model to include a field for expose headers and update the CORS policy generation logic to incorporate this new field.

*   Update the `CorsConfig` struct:
    *   Add a field `AccessControlExposeHeaders` of type `[]string` to hold the list of response headers that should be exposed.
    *   Location: `adapter/internal/oasparser/model/mgw_swagger.go`.
    *   Signature: `AccessControlExposeHeaders []string `mapstructure:"accessControlExposeHeaders"`.

*   Modify the `getCorsPolicy` function:
    *   Location: `adapter/internal/oasparser/envoyconf/routes_with_clusters.go`.
    *   Signature: `getCorsPolicy(corsConfig *model.CorsConfig) *routev3.CorsPolicy`.
    *   When `corsConfig.AccessControlExposeHeaders` is non-empty:
        *   Set `corsPolicy.ExposeHeaders` to the headers joined as a comma-separated string.
    *   When `corsConfig.AccessControlExposeHeaders` is empty or nil:
        *   Ensure `corsPolicy.ExposeHeaders` is not set (or set to an empty string).

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.