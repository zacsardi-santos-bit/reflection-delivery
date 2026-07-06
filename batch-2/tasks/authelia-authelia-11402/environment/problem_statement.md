## Description

Authelia supports being deployed at a URL subpath (e.g., `https://example.com/auth`) rather than at the root. There are two issues with the current implementation of this feature that need to be fixed.

### Issue 1: Unhelpful validation error message

When a user configures the server to listen on a path that contains multiple segments (e.g., `/auth/` or `/auth/admin`), a validation error is produced. However, the current error message is vague — it only says the path "must not have a path with a forward slash" without showing what the correct format looks like or why the configured value is wrong.

The error message should be improved to show the user both a concrete example of the correct single-segment format and the problematic value that was configured, so it is immediately clear what needs to be changed.

### Issue 2: Path-stripping middleware matches too broadly

The middleware that strips the configured base path from incoming requests currently uses a plain string prefix match. This means a configured subpath like `/a` could incorrectly match and process requests intended for paths like `/api/example`, because `/api/example` starts with the characters `/a`.

The middleware should only match requests where the URI genuinely begins with the configured subpath as a complete segment — that is, the URI must be exactly the path, or the path followed by a path separator, query string, or end of string. Short paths must not accidentally match longer unrelated paths.

Additionally, the middleware should handle edge cases: when no meaningful path is configured (empty or just a slash), it should be a no-op, and paths without a leading slash should be normalized automatically.

## Expected Behavior

- When an invalid multi-segment path is configured, the error message should include an example of the correct single-segment format alongside the problematic value.
- A configured subpath of `/a` must not match request URIs like `/api/example`.
- Configuring no subpath or a root path should result in no path-stripping behavior.
- Paths provided without a leading slash should be handled the same as those with one.
