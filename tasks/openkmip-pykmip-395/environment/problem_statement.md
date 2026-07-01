## Description

The authentication structure in the library currently only supports a single credential per authentication object. However, the KMIP protocol allows multiple credentials to be included in a single authentication block — for example, combining a username/password credential with a device credential. The current implementation cannot represent or encode this correctly.

## Expected Behavior

- An authentication object should be able to hold a list of zero or more credentials, not just a single credential.
- Creating an authentication object without any credentials should result in an empty credentials list by default.
- It should be possible to create an authentication object with one or more credentials of different types (username/password, device, etc.).
- Reading an authentication message from a binary stream should populate the credentials list with all credentials present in the encoding, including multiple credentials in sequence.
- Writing an authentication object with multiple credentials should produce a byte-accurate encoding containing all of them in order.
- If credentials are missing when reading or writing, a descriptive error should be raised.
- Attempting to set credentials to an invalid value (non-list, or list containing non-credential items) should raise a clear, descriptive error.
- The authentication object should support equality comparison, as well as readable string and representation formats that include all credentials.

## Why This Matters

Multi-credential authentication is part of the KMIP specification and is tested against standard KMIP test vectors. Without this support, clients cannot construct or parse authentication blocks that include more than one credential, limiting interoperability with compliant KMIP servers.
