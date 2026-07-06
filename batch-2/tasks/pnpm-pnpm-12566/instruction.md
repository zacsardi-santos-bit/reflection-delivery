I'm working on a registry proxy server that has a local vulnerability database.

*   The method previously named `validate_tarball_name` on `PackageName` must be removed and replaced by `parse_tarball_name`. The new method has the same validation logic but returns `Result<(String, String), RegistryError>` where the `Ok` variant is a tuple of `(canonical_filename, version)`. The canonical filename is the short basename form (e.g. `node-20.0.0.tgz`) and the version is the semver string extracted from the filename. All call sites previously using `validate_tarball_name` must be updated to use `parse_tarball_name`.

*   The `OsvIndex` struct must expose an `is_vulnerable(&self, package: &str, version: &str) -> bool` method that returns `true` when the given package@version is present in the vulnerability database and `false` otherwise.

*   When the OSV database is enabled, a GET request for a package's packument (e.g. `GET /foo`) must filter the JSON response to remove any version entry from `versions` whose key matches a vulnerable version, remove the corresponding entries from the `time` map, and remove any `dist-tags` entries whose target version was removed.

*   The packument filtering must also remove version entries where the version key does not match the `version` field inside the entry (identity mismatch) and entries with non-semver keys that cannot be validated, treating them as ineligible regardless of OSV status.

*   Packument filtering must apply equally to freshly proxied responses and to subsequent requests served from the cache; a second request for the same package must return the same filtered packument.

*   When the OSV database is enabled, a GET request for a specific version manifest (e.g. `GET /foo/1.1.0`) must return HTTP 404 if that version was removed by OSV filtering.

*   When the OSV database is enabled, a GET request for dist-tags (e.g. `GET /-/package/foo/dist-tags`) must return only the tags whose target versions were not removed by OSV filtering.

*   When the OSV database is enabled, a GET request for a tarball of a vulnerable version (e.g. `GET /foo/-/foo-1.0.0.tgz`) must return HTTP 403 Forbidden without contacting the upstream registry. The response body must contain the relevant advisory ID(s) that flagged this version.

*   Tarball vulnerability screening must be performed before any upstream fetch; the upstream must not receive a request for a tarball that is already known to be vulnerable.

*   When a tarball was previously cached before OSV was enabled, subsequent requests with OSV enabled must still return HTTP 403 Forbidden for vulnerable versions rather than serving the cached content.

*   The authentication access gate must be checked before OSV screening for tarballs. If a package requires authentication and the request is unauthenticated, the server must return HTTP 401 Unauthorized rather than HTTP 403 Forbidden.

*   When a scoped package tarball is requested using a URL-encoded full-name filename (e.g. `/@types/node/-/%40types%2Fnode-20.0.0.tgz`), the server must canonicalize the filename to its short form (`node-20.0.0.tgz`) before fetching from upstream and before writing to the cache. Both the canonical and non-canonical URL forms must return HTTP 200 with identical content. The upstream must be contacted exactly once. The cached file must exist at the canonical path (`.pnpr-cache/@types/node/node-20.0.0.tgz`) and must not exist at any path derived from the non-canonical encoded form.


*   Interface details: Type: Method
Name: parse_tarball_name
Location: pnpr/crates/pnpr/src/package_name.rs
Signature: fn parse_tarball_name(&self, filename: &str) -> Result<(String, String), RegistryError>
Description: Validates that `filename` is a plausible tarball name for this package and returns a tuple of (canonical_filename, version) on success. The canonical filename is the short basename form (e.g. `node-20.0.0.tgz` for `@types/node`). The version is the semver string extracted from the filename. Accepts both the short basename form and the full-name/URL-encoded form (e.g. `@types%2Fnode-20.0.0.tgz`). Rejects filenames for other packages, path traversal prefixes, and filenames missing the `.tgz` extension. This method replaces the previously existing `validate_tarball_name` (which returned `Result<(), RegistryError>` and discarded the parsed data) — it must replace every call site that referenced `validate_tarball_name`, including in the server's tarball-serving handler where the canonical filename and extracted version string are both used independently.

Type: Method
Name: is_vulnerable
Location: pnpr/crates/pnpr/src/resolver/osv/ (on the OsvIndex struct)
Signature: fn is_vulnerable(&self, package: &str, version: &str) -> bool
Description: Returns true if the given package@version combination is listed as vulnerable in the loaded OSV index, and false otherwise. Package name lookup must be case-insensitive and version matching must respect semver ranges recorded in the advisory data.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.