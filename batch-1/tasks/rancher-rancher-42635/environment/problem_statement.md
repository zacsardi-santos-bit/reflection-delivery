## Description

The LDAP authentication provider's core functions are untestable in isolation because they create their own external connections and directly access internal data sources. This tight coupling means there is no way to write unit tests for the user login flow or configuration retrieval without standing up a real LDAP server and a live Kubernetes API, making the code fragile and hard to maintain.

## Expected Behavior

- The user login function should accept an already-established connection to the LDAP server as a parameter, rather than creating its own connection internally. This allows callers to inject a mock connection for testing.
- A standalone helper function should be extracted from the user authentication method to convert untyped input into a typed login credential object. This function should return an error describing an unexpected input type when given invalid input.
- The configuration retrieval function should accept an injectable data access client as a parameter, rather than using a hardcoded internal reference. This allows callers to inject a mock client for testing.
- All calling sites of these functions must be updated to pass the appropriate connection or client they already have available.

## Why This Matters

Without these changes, writing unit tests for authentication scenarios — successful logins, invalid credentials, and access-denied responses — requires a running external LDAP server. Decoupling connection management from business logic makes the code both more testable and cleaner by separating concerns.
