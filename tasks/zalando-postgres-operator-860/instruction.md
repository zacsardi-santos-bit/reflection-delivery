Implement the ability to detect the actual PostgreSQL version running inside a cluster's containers by extracting it from the container's internal configuration. Update the operator to preserve the running version if it differs from the desired version during StatefulSet synchronization.

*   Implement the function `extractPgVersionFromBinPath` in `pkg/cluster/k8sres.go`:
    *   Accept a binary directory path string and a format template string.
    *   Extract and return the PostgreSQL version substring (e.g., "9.6" or "12") as a string.
    *   Return a non-nil error if the version cannot be parsed.
    *   Handle decimal versions: given the path "/usr/lib/postgresql/9.6/bin" and the default template, return "9.6".
    *   Handle integer versions: given the path "/usr/lib/postgresql/12/bin" and the default template, return "12".
    *   Work with alternative path formats: given the path "/usr/pgsql-12/bin" and the template "/usr/pgsql-%v/bin", return "12".

*   Ensure the constant `pgBinariesLocationTemplate` is set to "/usr/lib/postgresql/%v/bin" to correctly parse both decimal and integer version values.

*   Implement the method `getNewPgVersion` on the `Cluster` type in `pkg/cluster/k8sres.go`:
    *   Accept a Kubernetes container spec (`v1.Container`) and a desired new version string.
    *   Search the container's environment variables for the `SPILO_CONFIGURATION` variable.
    *   Parse the JSON value of `SPILO_CONFIGURATION` to extract the `bin_dir` field under the `postgresql` key.
    *   Use `extractPgVersionFromBinPath` with `pgBinariesLocationTemplate` to derive the currently running PostgreSQL version from the `bin_dir` value.
    *   Return the running version if it differs from the requested new version, logging a warning. Return the requested version if they match.
    *   Return a non-nil error if the `SPILO_CONFIGURATION` value is missing, malformed, or the version cannot be extracted.

*   Update references in the existing codebase:
    *   Change references from a top-level `PgVersion` field on the PostgreSQL spec to the `PgVersion` field nested inside the `PostgresqlParam` sub-struct to ensure the package compiles and all existing tests pass.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.