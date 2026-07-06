## Description

There are two related issues with the JWS library that need to be addressed:

**Security Fix: Panic on malformed JSON JWS**

When a JWS message in JSON format (not compact/dot-separated format) is missing its protected header field, the library panics instead of handling the input gracefully. This crash can be triggered by a specially crafted or malformed input, which could allow an attacker to cause a denial-of-service by sending such a message to any service that parses JWS tokens.

The fix should allow parsing to succeed on such inputs — even though they are incomplete or invalid for verification purposes. When subsequently attempting to verify such a message, the verification must fail cleanly rather than panicking. Note that this only applies to the JSON serialization format; the compact format (three dot-separated base64 segments) should still return an error when the header segment is missing or empty.

**New Feature: Distinguishing verification errors from other errors**

Currently there is no way to tell whether an error returned by the verify function was the result of an actual cryptographic verification failure versus some other kind of error (e.g., structural issues with the message, failure to fetch a key, etc.). Callers need a way to distinguish these cases so they can respond appropriately.

## Expected Behavior

- Parsing a JSON-format JWS with a missing protected header should succeed without error
- Verifying such an incomplete message should fail with a clear error
- A utility function should be available to check whether an error is specifically a cryptographic verification failure, returning false for non-verification errors

## Why This Matters

Panicking on malformed inputs is a security concern in any service that accepts untrusted JWS tokens. Additionally, being able to distinguish verification failures from other errors allows callers to make better decisions, such as retrying key fetches versus reporting a cryptographic failure.
