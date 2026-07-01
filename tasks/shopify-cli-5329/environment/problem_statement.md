## Description

The theme development server currently relies on a third-party HTTP library for making fetch requests and managing response objects. This creates unnecessary coupling to an external dependency and prevents using the native, built-in fetch capabilities that modern runtimes provide. In addition, the server's response-patching utilities modify server state in place (by mutating the outgoing response on a request event), which makes the data flow harder to reason about and test.

Beyond the dependency migration, there are significant gaps in error handling during theme development:

- When the storefront renderer cannot handle a route (returning a 4xx response, e.g., for account-related or app-specific routes), the server does nothing useful — it should fall back to proxying the request directly to the store.
- When a render attempt fails entirely due to a network error, the server does not present a helpful error page — developers are left without feedback.
- When theme file uploads fail, the server should display an error page listing the failed files and their errors, and the page should include the hot-reload script so developers see updates immediately when the issue is resolved.

## Expected Behavior

- The dev server uses the platform's built-in fetch API instead of a third-party HTTP library.
- Response-patching returns a new Response object rather than mutating the HTTP event.
- When rendering returns a 4xx status, the server falls back to proxying; if the proxy succeeds (status below 400), its response is returned; otherwise, the original render response is forwarded.
- Network errors during rendering produce an HTML error page (status 502) with the hot-reload script injected.
- Upload errors produce an HTML error page listing the specific file errors, without attempting a render, also with the hot-reload script injected.
- A pair of utility functions for creating and extracting information from fetch errors are available for reuse across the server's error-handling paths.

## Why This Matters

Developers using the theme dev server currently get poor feedback when things go wrong — redirects, auth-gated pages, and network failures can all result in silent failures or confusing behavior. This change makes error recovery predictable and ensures developers always see actionable feedback.
