## Description

The current implementation of proxy access authorization in our token system has two related issues:

1. The format for specifying allowed proxy principals is a single string, which limits us to identifying exactly one allowed proxy per authorization detail entry. This should be changed to a list format so that multiple allowed proxy principals can be specified in a single entry.

2. When validating a certificate-bound access token for proxy use, the system does not currently verify the certificate's identity against the allowed proxy principals listed in the authorization details. This gap means the proxy access claim in the token is not being cross-checked against the presenting certificate's identity.

## Expected Behavior

- The proxy access authorization details format should accept a list of principals rather than a single string, allowing one or more principals to be named.
- A new utility should be available to extract a single, unambiguous identity URI from an X.509 certificate. If the certificate has no such identity, or more than one, the utility should return nothing, since the identity is ambiguous.
- When a certificate-bound access token is verified and the token carries proxy access authorization details, the system should compare the certificate's identity URI against the list of allowed principals. If the identity matches any entry in the list, the verification succeeds. If the authorization details are missing, malformed, use an incorrect format, or contain no match for the certificate's identity, verification should fail with a confirmation error.

## Why This Matters

Without these changes, the proxy access claim in a token cannot be cryptographically bound to the certificate used to present it, which is a security gap. The list format also makes the system more flexible, allowing multiple valid proxy principals to be declared in a single authorization detail.
