## Description

EKS Anywhere relies on bundle manifests to determine which component versions are used during cluster provisioning and upgrades. Currently, nothing prevents these bundles from being silently tampered with — a forged or altered bundle could cause the system to deploy unintended components without any warning.

We need to introduce cryptographic signature verification for bundles. When the system fetches a bundle, it should verify that the bundle's contents have not been changed since it was signed by a trusted source. Verification should be based on a digital signature stored in the bundle's metadata annotations, checked against a known public key. Only the stable, meaningful fields of the bundle should be included in the digest computation — volatile fields such as creation timestamps, mutable annotations, and certain per-provider component fields should be excluded.

## Expected Behavior

- A new component should be available to verify a bundle's signature, accepting the bundle and a public key, and returning whether the bundle is valid along with any error details.
- When a bundle is missing its signature annotation, the error should clearly indicate the annotation is absent.
- When the signature is present but malformed (non-base64, invalid key format, etc.), descriptive errors should be returned for each failure mode.
- A new validation function should check whether a cluster's Kubernetes version has extended support, using bundle signature verification as a prerequisite.
- If the cluster's bundle cannot be found, or if its signature fails verification, cluster reconciliation should fail with a clear, descriptive error.

## Why This Matters

Without this change, there is no guarantee that the bundle being used during cluster operations is the authentic, unmodified one produced by the EKS Anywhere release pipeline. Adding signature verification ensures the integrity of bundles and prevents tampered manifests from silently influencing cluster behavior.
