Implement improvements to the Shopify CLI theme development server by migrating to the native fetch API, enhancing error handling, and modifying response handling. Ensure the server provides clear feedback during development.

*   Replace third-party HTTP library usage with the native fetch API in all theme environment utilities.
*   Modify response-patching:
    *   Update `patchRenderingResponse` in `packages/theme/src/cli/utilities/theme-environment/proxy.ts` to accept `DevServerContext` and `Response` as arguments, removing the H3 event parameter.
    *   Return a `Promise<Response>` with HTML containing replaced CDN paths, and updated `link` and `set-cookie` headers.
*   Enhance error handling:
    *   Implement `createFetchError` in `packages/theme/src/cli/utilities/errors.ts` to create structured fetch error objects from Response-like objects or Errors.
    *   Implement `extractFetchErrorInfo` in the same file to extract displayable information from FetchErrors or plain Errors.
*   Update rendering and proxy behavior:
    *   Modify `render` in `packages/theme/src/cli/utilities/theme-environment/storefront-renderer.ts` to use global fetch, ensuring the `Content-Type` header is deleted post-fetch.
    *   Ensure hot-reload render handler returns a `Response` with patched HTML accessible via `.text()`.
    *   Use `URL` objects for proxy requests, ensuring correct URL construction.
*   Implement fallback and error page behavior:
    *   When a 4xx response is returned by the storefront renderer, proxy the request and return the proxy response if successful, otherwise return the original response.
    *   On network errors during rendering, return a 502 HTML error page with `hotReloadScript`.
    *   For theme file upload errors, return an HTML error page listing errors and including `hotReloadScript`, without calling `render`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.