I'm working on the Python serverless runtime for Vercel.

*   The normalize_event_header_pairs function must inject an 'x-vercel-oidc-token' header into the returned header list when the VERCEL_OIDC_TOKEN environment variable is set and non-empty and no 'x-vercel-oidc-token' header is already present in the input headers.

*   When VERCEL_OIDC_TOKEN is set to an empty string, normalize_event_header_pairs must NOT inject an 'x-vercel-oidc-token' header.

*   When neither the VERCEL_OIDC_TOKEN environment variable nor any request-level OIDC header is present, normalize_event_header_pairs must NOT inject an 'x-vercel-oidc-token' header.

*   When the input headers include 'x-vercel-internal-oidc-token' and VERCEL_OIDC_TOKEN is set, the internal header's value must take precedence over the environment variable value. The resulting header list must include ('x-vercel-oidc-token', <internal-token-value>) and must NOT include ('x-vercel-internal-oidc-token', <value>).

*   When the input headers already include 'x-vercel-oidc-token' (alongside 'x-vercel-internal-oidc-token' and VERCEL_OIDC_TOKEN in the environment), the public token value is preserved as-is. The output must contain exactly one 'x-vercel-oidc-token' entry with the public token value, and no 'x-vercel-internal-oidc-token' entry.

*   When the runtime is started with VERCEL_OIDC_TOKEN set to a non-empty value, HTTP handler apps, WSGI apps, and ASGI apps must all receive the token value via the 'x-vercel-oidc-token' request header on incoming requests that do not already carry that header.


*   Interface details: Type: Function
Name: normalize_event_header_pairs
Location: python/vercel-runtime/src/vercel_runtime/headers.py
Signature: normalize_event_header_pairs(headers: object) -> list[tuple[str, str]]
Description: Normalizes raw incoming event headers into a list of (key, value) string tuples. Must read the VERCEL_OIDC_TOKEN environment variable and inject it as an ("x-vercel-oidc-token", <value>) tuple when the token is non-empty and no "x-vercel-oidc-token" header is already present in the input. Must also handle the internal "x-vercel-internal-oidc-token" header by converting it to the public "x-vercel-oidc-token" header (stripping the internal one) when no public token already exists. The function is imported from vercel_runtime.headers.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.