## Description

The Azure Key Vault certificates client currently has no fake/mock server support, which means developers cannot write unit tests for code that uses the certificates client without connecting to a real Azure Key Vault. Other Key Vault clients (for keys and secrets) already have this capability, but the certificates client is missing it.

## Expected Behavior

- A fake server package should be available alongside the certificates client that allows developers to configure stub responses for every certificate operation.
- Developers should be able to instantiate a certificates client that talks to the fake server instead of a real cloud endpoint, with zero network calls.
- All certificate operations should be testable offline: backup, create, delete, get, import, list (with pagination), merge, purge, recover, restore, update certificates; manage certificate operations, policies, contacts, and issuers.
- Paging operations should support multi-page stub responses so paginated list behavior can be tested end-to-end.
- The fake server should correctly propagate request parameters (such as expiry dates and contact lists) into its responses when the handler logic specifies it.

## Why This Matters

Without fake server support, developers must run integration tests against real Azure infrastructure to validate any code that uses the certificates client. This makes local development slow, requires cloud credentials in CI, and blocks testing in offline or restricted environments. Adding fake support brings the certificates client in line with the rest of the Key Vault SDK and enables fast, reliable, credential-free unit tests.
