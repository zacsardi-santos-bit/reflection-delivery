Refactor the LDAP authentication provider to improve testability by decoupling external dependencies from core functions. Implement parameterized dependency injection for LDAP connections and data access clients, and extract a helper function for input conversion.

*   Update the `loginUser` method in `pkg/auth/providers/ldap/ldap_client.go`:
    *   Accept an `ldapv3.Client` as the first parameter instead of creating the connection internally.
    *   Maintain existing parameters: `credential` (*v32.BasicLogin), `config` (*v3.LdapConfig), and `caPool` (*x509.CertPool).
    *   Return the user's principal and group principals with specific formats on successful authentication.
    *   Return an empty `v3.Principal{}`, nil group principals, and a non-nil error on authentication failure or access denial.

*   Implement a new function `toBasicLogin` in `pkg/auth/providers/ldap/ldap_provider.go`:
    *   Signature: `toBasicLogin(input interface{}) (*v32.BasicLogin, error)`.
    *   Return the input as a `*v32.BasicLogin` and nil error if the input is of the correct type.
    *   Return nil and an error with the message "unexpected input type" for invalid input types.

*   Modify the `getLDAPConfig` method in `pkg/auth/providers/ldap/ldap_provider.go`:
    *   Accept an `objectclient.GenericClient` as a parameter to retrieve the LDAP configuration.
    *   Return the `LdapConfig` with the `Certificate` field populated, the `x509.CertPool`, and any error.

*   Update all call sites of `loginUser` and `getLDAPConfig` throughout the package:
    *   Ensure they pass the appropriate `ldapv3.Client` or `objectclient.GenericClient` instance they have available.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.