Implement a middleware factory function to serve static files for an authentication frame in the Ghost admin panel. Ensure the middleware handles file requests securely and substitutes placeholders with the actual site URL. Additionally, relocate the frontend analytics script to a new directory.

*   Export a factory function `createServeAuthFrameFileMw` from `ghost/core/core/server/web/admin/middleware/serve-auth-frame-file.js`.
    *   The function should accept a `config` object and a `urlUtils` object.
    *   Return an async Express middleware function `(req, res, next)`.

*   Middleware behavior:
    *   For requests to the root URL `/`, serve `index.html` from `{publicFilePath}/admin-auth/`.
        *   Read the file, convert it to a string, and respond with `res.end(content)`.
    *   For requests to `/{filename}`, serve the file from `{publicFilePath}/admin-auth/` using only the basename of the URL path.
        *   Prevent directory traversal by ignoring path components other than the filename.
    *   Replace `{{SITE_ORIGIN}}` in file content with the site URL from `urlUtils.getSiteUrl()` before responding.
    *   If a file cannot be read, call `next()` without calling `res.end()`.

*   Relocate the `ghost-stats.js` file:
    *   Ensure it is present at `ghost/core/core/frontend/src/ghost-stats/ghost-stats.js`.
    *   Update any references to the old path `ghost/core/core/frontend/public/ghost-stats.js` to the new location.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.