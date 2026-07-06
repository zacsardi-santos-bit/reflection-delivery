Implement a secure API route in the Next.js documentation app to allow external services to trigger cache revalidation for specific tags. Ensure the route requires an API key in the authorization header and handles two tiers of API keys with appropriate rate-limiting and error handling.

*   Implement the `_handleRevalidateRequest` function in `apps/docs/app/api/revalidate/route.ts` with the following behavior:
    *   Return a 401 response with 'Missing Authorization header' if the request lacks an Authorization header.
    *   Return a 401 response with 'Invalid Authorization header' if the Authorization header contains an unrecognized Bearer token.
    *   Return a 400 response with 'Malformed request body' if the request body lacks a 'tags' array property.
*   Handle API key tiers:
    *   For basic-tier keys (from `DOCS_REVALIDATION_KEYS`):
        *   If no revalidation occurred in the last 6 hours, call `revalidateTag` for each tag and return a 204 response.
        *   If revalidation occurred within the last 6 hours, return a 429 response with 'revalidated within the last 6 hours'.
    *   For override-tier keys (from `DOCS_REVALIDATION_OVERRIDE_KEYS`):
        *   Bypass the 6-hour rate limit, call `revalidateTag` for each tag, and return a 204 response.
*   Use a Supabase client (configured with `NEXT_PUBLIC_SUPABASE_URL` and `SUPABASE_SECRET_KEY`) to:
    *   Query recent revalidation history with the `rpc` method.
    *   Log new revalidation events using the `from().insert()` method.
*   Extract authorization tokens from 'Authorization: Bearer <token>' headers.
*   Ensure `_handleRevalidateRequest` is exported as a named export.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.