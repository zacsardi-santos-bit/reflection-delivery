Implement a fake server package for the Azure Key Vault certificates client in the Go SDK to enable offline unit testing. Ensure the fake server can handle all certificate operations with stub responses and support paginated list operations.

*   Create a new Go package named 'fake' under `sdk/security/keyvault/azcertificates/fake/`.
    *   Export a `Server` struct with 27 function fields, each corresponding to a client operation. Function signatures must match those specified in the interface.
    *   Export a `NewServerTransport(*Server)` function that returns a `*ServerTransport`. This must implement the `azcore.Transporter` interface.

*   Implement the `ServerTransport` to:
    *   Intercept HTTP requests and route them to the appropriate `Server` function field.
    *   Return configured responses or an error for unhandled operations.
    *   Support multi-page responses for pager operations using `azfake.PagerResponder[T]`.

*   Modify `sdk/security/keyvault/azcertificates/client.go`:
    *   Embed the operation name into the request context using `runtime.CtxAPINameKey{}` for every public method and pager Fetcher closure.
    *   Format operation names as `Client.<MethodName>` (e.g., `Client.BackupCertificate`).

*   Ensure the fake transport handles operations correctly:
    *   `BackupCertificate` returns a non-nil `Value`.
    *   `CreateCertificate` returns an ID with `Name()` equal to the certificate name and `Version()` as 'pending'.
    *   `DeleteCertificate` and related operations return IDs with correct `Name()`.
    *   `GetCertificate` with an empty version string returns an ID with an empty `Version()`.
    *   `ImportCertificate` propagates `CertificateAttributes.Expires` from request to response.
    *   `MergeCertificate` returns correct name and version.
    *   `PurgeDeletedCertificate` returns an empty response body.
    *   `SetContacts` echoes back the provided contact list.

*   Implement the `azcertificates.ID` type to:
    *   Parse Key Vault certificate URLs and expose `Name()` and `Version()` methods.
    *   Return an empty string for `Version()` when the URL lacks a version segment.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.