## Description

Deprecating or undeprecating an already-published package version is broken. When a package manager sends a metadata-only update to mark a version as deprecated — which involves a re-PUT of the packument with no tarball attachment and the same distribution checksum as the already-published version — the registry incorrectly rejects the request with a conflict error. This completely breaks the deprecation workflow for any package hosted in the registry.

## Expected Behavior

- A metadata-only re-PUT that only changes the deprecation notice on an existing version should be accepted successfully.
- The version's resolution-critical fields (checksum, download location, etc.) must be preserved from the originally-published version — a metadata-only update must not be able to overwrite them.
- Malformed version entries sent in a metadata-only update must be ignored; they must not corrupt or erase the already-published version's data.
- Metadata-only requests that try to introduce a brand-new version entry (without uploading an actual tarball) must be rejected, since they would create a version record pointing to a tarball that does not exist.

## Why This Matters

Teams relying on the registry to host packages need to be able to deprecate old versions to signal to consumers that they should upgrade. When the deprecation workflow is broken, users of those packages receive no guidance and may continue using vulnerable or abandoned versions. Additionally, the new guardrails prevent security-relevant scenarios such as a client silently rewriting the resolution metadata (e.g., the checksum) of an already-published version through a metadata-only update.
