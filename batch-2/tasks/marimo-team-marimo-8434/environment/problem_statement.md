## Description

When mounting the marimo ASGI app inside a larger web framework at a sub-path that differs from the path configured for the dynamic directory, all requests to individual notebooks return 404. For example, configuring the directory browser for one URL prefix and mounting the whole marimo ASGI app under a different outer prefix means that any request to access a notebook fails silently instead of loading.

There is also a related bug: the URL passed internally to each notebook sub-app for constructing its own links is a raw filesystem path instead of a proper web URL path. This breaks asset loading and any navigation within the notebook when the app is served under a nested URL prefix.

## Expected Behavior

- When the dynamic directory browser is configured with one path and the whole app is mounted at a different outer prefix, notebooks should be accessible at the combined URL.
- Trailing-slash redirects must include the full correct URL path including all outer prefixes.
- Assets within a notebook must load when the app is nested under multiple URL prefixes.
- Nested directory notebooks must be reachable through the combined URL.
- The URL passed to each notebook app for building its own links must be a proper web URL path, not a filesystem path.
- When the directory browser is configured without any path prefix at all, the system should raise a clear error immediately rather than silently misbehaving.

## Why This Matters

Users who integrate marimo into their existing web applications commonly mount the entire marimo ASGI app under a specific route prefix. This scenario was completely broken when the outer mount prefix and the inner directory path differed. The result was a confusing 404 on every notebook and broken internal URLs.
