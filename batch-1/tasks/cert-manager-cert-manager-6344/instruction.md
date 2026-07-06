Update the cert-manager's Venafi integration to use the latest major version of the Venafi certificate management client library. Ensure all functionalities, such as certificate requests and retrievals, continue to work seamlessly after the upgrade.

*   Update all source files in the Venafi client packages to import from version 5 of the vcert library:
    *   Use `github.com/Venafi/vcert/v5` instead of `github.com/Venafi/vcert/v4`.
*   Modify the `go.mod` file:
    *   Declare a dependency on `github.com/Venafi/vcert/v5`.
*   Update the connector interface:
    *   Use types from `github.com/Venafi/vcert/v5/pkg/certificate` and `github.com/Venafi/vcert/v5/pkg/endpoint`.
    *   Ensure methods like `Ping`, `ReadZoneConfiguration`, `RequestCertificate`, `RetrieveCertificate`, and `RenewCertificate` use v5 types.
*   Revise the `configForIssuer` function:
    *   Return a `*vcert.Config` from `github.com/Venafi/vcert/v5`.
    *   Populate credentials for TPP and Cloud issuers using Kubernetes secrets.
*   Amend the `Venafi` struct in `pkg/issuer/venafi/client/venaficlient.go`:
    *   Implement methods using vcert/v5 types.
    *   Ensure `RequestCertificate` and `RetrieveCertificate` methods handle errors and return appropriate values.
*   Adjust the `Connector` struct in `pkg/issuer/venafi/client/fake/connector.go`:
    *   Embed and delegate to `*fake.Connector` from `github.com/Venafi/vcert/v5/pkg/venafi/fake`.
    *   Use v5 types for all method signatures.
*   Ensure the controller packages for `certificaterequests/venafi` and `certificatesigningrequests/venafi`:
    *   Import `github.com/Venafi/vcert/v5/pkg/endpoint` for any endpoint types used in tests.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.