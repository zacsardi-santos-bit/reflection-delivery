## Description

When a TLS client attempts to connect without having pre-loaded key material and without providing a credential reload configuration, the connection is incorrectly rejected with an error. This is wrong behavior because clients — unlike servers — do not always need to present their own certificates. A TLS client may only need to verify the server's identity, and in that case, having no client-side key material should be acceptable.

Additionally, there are no existing unit tests that verify the correct storage and retrieval of TLS key material configuration after it has been set — including the PEM root certificate and private key/certificate pairs.

## Expected Behavior

- When fetching key materials for a TLS **client** that has no pre-loaded key materials and no credential reload config, the operation should succeed (return OK status).
- When fetching key materials for a TLS **server** with no pre-loaded key materials and no credential reload config, the operation should continue to fail with a precondition error (servers must have credentials).
- After setting key materials on a TLS configuration object, querying it should return the exact root certificate, private key, and certificate chain that were set.

## Why This Matters

The current behavior incorrectly treats clients as if they must always have pre-loaded key material, just like servers do. This prevents valid TLS client configurations from working correctly. Fixing this allows TLS clients that rely on server-side certificate verification (without providing client certificates) to function properly.
