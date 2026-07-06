## Description

The OAuth 2.0 specification defines two client types: "confidential" clients (server-side applications that can securely store a client secret) and "public" clients (mobile apps, single-page applications, etc., that cannot safely store secrets). Currently, the library treats all OAuth applications as if they require a client secret, which means public clients cannot properly authenticate and obtain tokens without providing a secret they may not have.

## Expected Behavior

- OAuth applications should have an explicit confidentiality flag that marks them as either confidential or public.
- A public (non-confidential) application should be able to obtain tokens using only its client identifier, without needing to supply a secret.
- If a public client provides an incorrect secret, the request should be rejected — the absence of a secret is allowed, but a wrong secret is not.
- Confidential applications must continue to require the full credentials (identifier + secret) to obtain tokens; requests without a secret from confidential clients should be rejected.
- Token revocation should respect the same distinction: tokens belonging to public applications can be revoked without client authentication, while tokens belonging to confidential applications should only be revoked when the correct client is authenticated.
- The database schema for applications must support storing the confidential flag, defaulting to treating existing applications as confidential to preserve backwards compatibility.
- The application must be considered invalid if the confidential flag is left undefined (neither true nor false).

## Why This Matters

Without this distinction, public clients are forced to either skip authentication entirely or to supply a dummy secret, both of which are incorrect per the OAuth specification. Supporting explicit confidentiality enables the library to properly enforce the correct authentication requirements per client type, improving security and standards compliance.
