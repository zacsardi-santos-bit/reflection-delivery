I'm working on improving Authelia's subpath deployment support and have found two bugs that need fixing.

*   The StripPath function must return a middleware such that when the configured path is empty or a single slash, calling the middleware with any handler returns that handler unchanged — meaning if nil is passed as the next handler, the result is nil.

*   The StripPath function must normalize the path by prepending a leading slash if the provided path does not already start with one (e.g., 'auth' becomes '/auth').

*   The StripPath function must match request URIs using pattern-based matching, not a simple string prefix check. A configured path of '/a' must NOT match URIs like '/api/example' or '/api/example?rd=123'. Only URIs that are exactly the configured path, or the configured path followed by '/', '?', or end of string, must match.

*   When a request URI matches the configured path, StripPath must set the request context user value for UserValueKeyBaseURL to the normalized path string (with leading slash) and the user value for UserValueKeyRawURI to the original full raw URI string before stripping.

*   When a request URI matches the configured path, StripPath must update the request URI by stripping the path prefix, leaving only the remainder (e.g., '/auth/example' with path '/auth' becomes '/example'; '/auth' with path '/auth' becomes empty which fasthttp normalizes to '/').

*   When a request URI does not match the configured path, StripPath must leave the request URI unchanged and must not set UserValueKeyBaseURL or UserValueKeyRawURI user values.

*   The server address validation error for a multi-segment path (e.g. '/auth/', '/auth/abc') must use the exact format: "server: option 'address' must be a single subpath (i.e. '<first_segment>'), but '<full_path>' contains multiple segments", where <first_segment> is the path up to and including only the first segment (e.g., '/auth' from '/auth/' or '/auth/abc') and <full_path> is the complete configured path.

*   The RouterPath() method on the server address type must return '/' when the server address has no path, an empty path, or a path of just '/'. It must return the exact path string (including any trailing slashes or multiple segments) when the address has a non-root path configured.


*   Interface details: Type: Function
Name: StripPath
Location: internal/middlewares/strip_path.go
Signature: StripPath(path string) Middleware
Description: Returns a Middleware (a function from fasthttp.RequestHandler to fasthttp.RequestHandler). When path is empty string or a single slash "/", the returned middleware must return its next handler argument unchanged (returning nil if nil is passed). When path does not start with "/", it must be normalized by prepending "/". The middleware uses pattern-based matching (not simple string prefix) to check whether the incoming request URI equals the path or is the path followed by "/", "?", or end of string — this prevents a path like "/a" from matching URIs like "/api/example". When a URI matches: sets the request context user value keyed by UserValueKeyBaseURL to the normalized path string, sets the user value keyed by UserValueKeyRawURI to the original raw URI string, and strips the path prefix from the request URI using strings.TrimPrefix. When a URI does not match: leaves the request URI unchanged and sets no user values.

Type: Constant
Name: UserValueKeyBaseURL
Location: internal/middlewares (existing constant in the middlewares package)
Description: String key used to store the matched base URL path in the request context user values when a subpath match occurs in StripPath.

Type: Constant
Name: UserValueKeyRawURI
Location: internal/middlewares (existing constant in the middlewares package)
Description: String key used to store the original raw request URI in the request context user values when a subpath match occurs in StripPath.

Type: Constant
Name: errFmtServerPathNotEndForwardSlash
Location: internal/configuration/validator/const.go
Description: Error format string used when the server address path contains multiple segments. Must be updated to the format: "server: option 'address' must be a single subpath (i.e. '%s'), but '%s' contains multiple segments" — where the first %s is the first path segment (e.g. "/auth" extracted from "/auth/" or "/auth/abc") and the second %s is the full problematic path.

Type: Function
Name: ValidateServerAddress
Location: internal/configuration/validator/server.go
Description: Validates the server address configuration. When the address path contains multiple segments (e.g. "/auth/", "/auth/abc"), must produce an error using errFmtServerPathNotEndForwardSlash formatted with the first segment (e.g. "/auth") as the example and the full path as the invalid value. The first segment is derived by splitting the path on "/" and taking the first two parts (index 0 and 1) to form "/<segment>".

Type: Method
Name: RouterPath
Location: Method on the server Address type (in the address or schema package used by config.Server.Address)
Signature: RouterPath() string
Description: Returns the path component of the server address for use by the router. When the address has no path configured, an empty path, or a path of just "/", must return "/". When the address has a non-root path configured (e.g. "/auth", "/auth/", "/auth/abc"), must return the exact path string as configured (including any trailing slash or multiple segments).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.