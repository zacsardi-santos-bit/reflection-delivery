## Description

The logging configuration subsystem needs the ability to inspect X.509 certificate data to determine trust chain relationships. Specifically, we need to be able to parse raw certificate bytes and determine whether a given certificate was issued (signed) by a particular certificate authority. This is foundational for rejecting configurations that submit self-signed certificates in contexts where they are not supported.

Currently, there is no utility in the logging utilities package that can load certificates from raw encoded byte input and check their signing relationships. Without this capability, the system cannot distinguish between certificates signed by a well-known authority and those that are self-signed.

## Expected Behavior

- Raw encoded certificate bytes should be parseable into one or more certificate objects.
- Given two certificate objects, the system should be able to correctly determine whether the first was signed by the second.
- A self-signed certificate checked against its own CA should be recognized as validly signed.
- A certificate signed by a third-party authority checked against that authority should be recognized as validly signed.
- A certificate checked against an unrelated authority should be identified as not signed by that authority.

## Why This Matters

Certain logging backends do not support self-signed certificates when client authentication is enabled. By being able to inspect certificate trust chains programmatically, the system can detect and reject such configurations early, providing clear feedback to users rather than allowing connections to fail at runtime with cryptic errors.
