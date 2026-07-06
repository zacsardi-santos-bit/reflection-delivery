## Description

When working with Certificate Transparency logs, a log client needs to build the correct "to-be-signed" (TBS) certificate data from a pre-certificate before embedding it in a Signed Certificate Timestamp (SCT). The existing function for building pre-cert TBS data does not correctly handle all cases involving an intermediate pre-issuer certificate and Authority Key Identifier (AKI) presence or absence.

## Problem

The current implementation incorrectly requires that both the pre-certificate and the pre-issuer must have an Authority Key Identifier extension, and fails with an error if either is missing. This is too restrictive. The function should gracefully handle all combinations:

- When neither the pre-certificate nor the pre-issuer has an AKI, the result should have no AKI.
- When the pre-certificate has an AKI but the pre-issuer does not, the AKI should be removed from the result.
- When the pre-certificate has no AKI but the pre-issuer does, the AKI should be added to the result with the pre-issuer's key identifier.
- When both have an AKI, the result AKI should be updated to the pre-issuer's key identifier.

## Expected Behavior

The function that takes the raw TBS data from a pre-certificate and an optional pre-issuer certificate should:

- Remove the CT poison extension from the resulting TBS data.
- If a pre-issuer certificate is provided and is a valid Certificate Transparency pre-issuer (has the required extended key usage), replace the issuer field with the pre-issuer's own issuer and update the AKI to reference the real issuer's key.
- If no pre-issuer is given, return the TBS with the original AKI preserved.
- Return an error if a pre-issuer is provided but lacks the required Certificate Transparency extended key usage marking.
- Handle all combinations of AKI presence or absence gracefully.
- Always return valid, parseable certificate TBS data.

## Why This Matters

Certificates issued via a CT pre-issuer intermediate may or may not include AKI extensions, and the log tooling must handle all these cases correctly. The current restriction causes valid pre-certificate submissions to be rejected unnecessarily.
