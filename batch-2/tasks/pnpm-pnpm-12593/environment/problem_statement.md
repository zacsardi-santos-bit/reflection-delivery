## Description

Our npm registry proxy rewrites tarball URLs from the upstream registry to point at itself, so clients always download through the proxy. Currently, when rewriting these URLs, the proxy ignores the actual filename declared by the upstream and instead constructs a new filename by combining the package name and version string in a predictable pattern. This causes a mismatch for packages that use non-standard tarball naming conventions — for instance, some older or forked packages use zero-padded or otherwise unusual filenames that do not match the version string. When the proxy rewrites those URLs to a "normalized" name, clients end up with a URL that doesn't correspond to what the upstream actually serves.

## Expected Behavior

- The proxy should preserve the exact tarball filename declared by each version's distribution metadata, so non-canonical upstream names survive into the client's lockfile and the cache.
- If the upstream URL contains a query string or URL fragment (e.g., a signed CDN URL), those should be stripped — only the path basename should appear in the rewritten URL served to clients.
- If a version's declared tarball URL has no usable filename (e.g., a bare directory path with a trailing slash), the proxy should fall back to constructing a conventional filename from the version information rather than passing through an unusable URL.
- If two versions in the same package declare the same tarball filename, the proxy must reject requests for that filename rather than silently binding the wrong version's integrity verification to the response.

## Why This Matters

Preserving upstream basenames ensures that what a client records in its lockfile exactly matches what the upstream hosts, preventing broken installs. Stripping query strings keeps the public-facing route stable and prevents signed or expiring URLs from leaking into the lockfile. Rejecting ambiguous basenames prevents a subtle integrity bypass where bytes from one version could be verified against another version's checksum.
