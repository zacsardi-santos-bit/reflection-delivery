I'm working on EKS Anywhere and need to add cryptographic signature verification for bundle manifests. Right now there's nothing stopping a tampered bundle from being used during cluster operations — we need a way to verify that bundles come from a trusted source and haven't been modified.

I need a new package that can verify a bundle's digital signature. The idea is: take the bundle, serialize it, strip out volatile or excluded fields (like creation timestamps, mutable annotations, and certain per-provider component fields), compute a digest of what remains, and then verify the signature stored in the bundle's annotations against a known public key. The function should return whether the bundle is valid and surface errors for each failure mode — missing signature, malformed base64, invalid key format, and so on.

I also need a validation function that uses this signature check as part of validating extended Kubernetes version support. It should take a cluster, its bundle, and a client, and return an error if the bundle's signature is missing or invalid.

Finally, the cluster reconciler should call this validation before proceeding with reconciliation. If the bundle can't be found or its signature is invalid, reconciliation should fail with a descriptive error.

The constants for the signature annotation key, the trusted public key, and the set of fields to exclude from the digest computation all need to be defined in the shared constants package.
