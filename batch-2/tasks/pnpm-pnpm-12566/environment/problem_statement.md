## Description

The pnpr registry proxy has a local vulnerability database integration that was originally wired only to the dependency resolver. The proxy itself — which handles packument and tarball requests — does not yet use this database to screen what it serves to clients.

As a result, clients that request package listings or download tarballs through the proxy can receive vulnerable versions even when the server has a local copy of the advisory data that would flag those versions.

## Expected Behavior

- When the vulnerability database is enabled, packument responses should omit any version that is listed as vulnerable, along with its timestamp entry and any distribution tags pointing to it.
- Version entries that contain identity inconsistencies (where the version string inside the entry does not match its key in the packument, or the key is not a valid semantic version) should also be excluded.
- Requests for a specific version manifest that has been filtered out should return a "not found" response.
- The distribution-tags endpoint should only expose tags whose target versions are still available.
- Requests to download a vulnerable tarball should be rejected before the upstream registry is ever contacted, with a response that identifies which advisories flagged that version.
- If a vulnerable tarball was cached before screening was enabled, it should still be blocked on subsequent requests.
- Authentication requirements must still be enforced before vulnerability checks — an unauthenticated request to a protected package should not reveal vulnerability information.
- The same filtering must apply consistently to both freshly proxied responses and cached responses.

There is also a separate bug with scoped package tarball URLs. When a client requests a scoped tarball using a URL-encoded full package name in the filename portion of the path, the server does not normalize the filename before fetching and caching. This causes the upstream to be contacted with a non-canonical filename and the cached file to be stored under the wrong path, breaking deduplication.

## Why This Matters

Without this integration, the proxy offers no protection against known-vulnerable packages even when all the advisory data is available locally. Clients behind the proxy should be shielded from vulnerable versions automatically, not just during dependency resolution.
