## Description

The Ghost admin panel needs a dedicated middleware to serve static files for an authentication frame — an isolated login interface that operates separately from the main admin application. Currently, no such middleware exists, which means the authentication frame has no way to serve its own static assets through the admin server.

## Expected Behavior

- A new middleware factory should be added to the admin middleware layer that can serve files from a specific auth-frame asset directory.
- When a request arrives for the root path, the middleware should serve the main HTML entry point from the auth directory.
- When a request arrives for any other path, the middleware should serve the corresponding file from the auth directory, using only the filename (not the full path) to prevent directory traversal attacks.
- Any placeholder for the site origin embedded in served files should be replaced at request time with the actual site URL, so the auth frame knows which origin it's operating on.
- If a requested file does not exist in the auth directory, the middleware should pass the request on to the next handler rather than returning an error.

As part of this same change, a frontend analytics script is being moved from its current location in the public assets directory to a new location within the frontend source tree. Tests referencing this script must be updated to use the new path.

## Why This Matters

Without this middleware, the admin authentication frame cannot load its own assets through the Ghost server. This blocks the ability to deliver an isolated, self-contained admin login experience. The site-origin substitution is important so the auth frame can communicate back to the correct host without hardcoding URLs into the distributed files.
