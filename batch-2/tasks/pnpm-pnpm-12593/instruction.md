I'm working on an npm registry proxy that rewrites tarball URLs so that clients always download through the proxy.

*   A new public function tarball_basename must be added to the upstream module. It must accept a URL string and return an Option containing the last path segment with any query string and URL fragment stripped. It must return None when the URL ends in a trailing slash or has no usable path segment.

*   tarball_basename must return Some with just the bare filename for URLs with query strings (e.g. '?sig=x' is removed), URLs with fragments (e.g. '#frag' is removed), absolute URLs with a non-empty last path segment, and plain relative basenames with no host component. It must return None for URLs ending in a trailing slash.

*   The rewrite_tarball_urls function must be updated to preserve the upstream tarball basename rather than constructing a URL from the package name and version string. When a version's dist.tarball URL has a usable basename, that basename must appear verbatim in the rewritten URL served to clients — including non-canonical names that do not follow the name-version.tgz convention.

*   When a dist.tarball URL contains a query string or URL fragment, rewrite_tarball_urls must strip the query/fragment so that only the path basename is retained in the rewritten URL (e.g., a URL like 'https://cdn.example.com/foo/-/foo-1.0.0.tgz?sig=abc&exp=123' rewrites to 'http://<host>/foo/-/foo-1.0.0.tgz').

*   When a dist.tarball URL has no usable basename (e.g., it ends with a trailing slash), rewrite_tarball_urls must fall back to constructing the tarball route from the version manifest's declared version field, following the conventional '<name>-<version>.tgz' pattern.

*   The server's tarball route must be determined by the declaring version's dist.tarball basename, not by the version string. If version 1.0.0 declares a dist.tarball whose basename is 'foo-2.0.0.tgz', then: (a) the rewritten public tarball URL for version 1.0.0 uses 'foo-2.0.0.tgz', (b) the upstream fetch uses that same path, (c) the cache stores the tarball under 'foo-2.0.0.tgz', and (d) integrity verification is bound to version 1.0.0's integrity field.

*   When two different versions in a packument declare the same tarball basename, a request for that tarball must be rejected with HTTP 502 Bad Gateway. The upstream tarball endpoint must not be contacted; only the packument endpoint is fetched to determine the conflict.


*   Interface details: Type: Function
Name: tarball_basename
Location: pnpr/crates/pnpr/src/upstream.rs (or pnpr/crates/pnpr/src/upstream/mod.rs)
Signature: tarball_basename(url: &str) -> Option<&str>
Description: Extracts the basename from a tarball URL, stripping any query string or URL fragment. Returns Some(basename) when the URL has a non-empty last path segment, or None when the URL has a trailing slash or no usable basename. Must be exported from the upstream module (pub fn).

Specific return value contract:
- tarball_basename("https://r/foo/-/foo-1.0.0.tgz")       → Some("foo-1.0.0.tgz")
- tarball_basename("https://r/foo/-/foo-1.0.0.tgz?sig=x") → Some("foo-1.0.0.tgz")  (query stripped)
- tarball_basename("https://r/foo/-/foo-1.0.0.tgz#frag")  → Some("foo-1.0.0.tgz")  (fragment stripped)
- tarball_basename("foo-1.0.0.tgz")                        → Some("foo-1.0.0.tgz")  (plain basename, no host)
- tarball_basename("https://r/foo/")                        → None                   (trailing slash, no segment)


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.