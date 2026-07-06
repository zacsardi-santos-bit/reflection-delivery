Add HTTP authentication support to the vulnerability scanner to enable it to access private Maven and npm registries. Implement multiple authentication schemes, including basic, bearer, and digest, and ensure credentials are read from Maven settings files and applied automatically.

* Implement the `HTTPAuthentication` struct in `internal/resolution/datasource/http_auth.go`:
    * Expose a `Get(ctx, httpClient, url)` method that returns `(*http.Response, error)`.
    * Ensure a nil receiver makes an unauthenticated GET request.
    * Include `Authorization` headers based on `AlwaysAuth`, `SupportedMethods`, and available credentials.
    * Implement digest authentication using the MD5-based algorithm (RFC 2617).

* Implement Maven settings parsing and authentication:
    * Implement `ParseMavenSettings(path string)` in `internal/resolution/datasource/maven_settings.go` to read and XML-decode Maven settings files.
        * Expand `${env.VAR_NAME}` placeholders using environment variables.
        * Return an empty `MavenSettingsXML` if the file is missing or unparsable.
    * Implement `MakeMavenAuth(globalSettings, userSettings MavenSettingsXML)` to return a map of server IDs to `HTTPAuthentication`.
        * User settings should override global settings for the same server ID.

* Update npm registry authentication:
    * Define `NpmrcConfig` as `map[string]string` in `internal/resolution/datasource/npmrc.go`.
    * Implement `ParseNpmRegistryInfo(npmrc NpmrcConfig)` to populate `NpmRegistryConfig.Auths`.
    * Implement `MakeRequest(ctx, httpClient, urlComponents...)` in `NpmRegistryConfig` to apply authentication and execute requests.

* Ensure `HTTPAuthMethod` constants use iota ordering:
    * `AuthBasic = 0`, `AuthBearer = 1`, `AuthDigest = 2`.

* Update `MavenRegistry` struct in `internal/resolution/datasource/maven_registry.go`:
    * Include `Parsed *url.URL` and `ID string` fields.
    * Modify `getArtifactMetadata` and `getVersionMetadata` methods to accept `MavenRegistry`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.