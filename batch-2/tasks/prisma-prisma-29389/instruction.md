I'm working on Prisma Studio and I want to stop relying on external CDN services to load the front-end assets.

*   The startStudioServer function must be exported from packages/cli/src/studio-server.ts and accept a single options object with three properties: handler (a function taking a Request and returning Response or Promise<Response>), onListen (a zero-argument callback invoked when the server is ready), and port (a number specifying which port to listen on).

*   startStudioServer must return a StudioServer object (also exported as a named type from the same file) that has a close() method to shut down the server.

*   When a GET request is received, startStudioServer must call the handler with the incoming Request and stream the resulting Response (status and body) back to the client.

*   When a HEAD request is received, startStudioServer must return the same HTTP status as the equivalent GET response but with an empty body.

*   When the handler function throws an error, startStudioServer must return an HTTP 500 response with the error message as the plain-text body, set the access-control-allow-origin response header to *, and log [Prisma Studio] followed by the error object to console.error.

*   The existing Studio BFF startup function (startStudioBff in packages/cli/src/Studio.ts) must be updated to call startStudioServer instead of the previously used serve function from @hono/node-server. The argument passed to startStudioServer must use a property named handler (not fetch) for the request-handling callback.

*   All BFF HTTP responses (including API, HTML, asset, and OPTIONS responses) must include the response header access-control-allow-origin set to *.

*   The HTML shell served at the root path (GET /) must include a stylesheet link tag pointing to /studio.css, a module script tag pointing to /studio.js, an inline script block setting window.__STUDIO_CONFIG__ to a JSON object reflecting the active adapter (e.g. {"adapter":"postgres"}), and must NOT contain any importmap, references to esm.sh, references to cdn.jsdelivr.net, or a reference to /adapter.js.

*   GET /studio.js must serve the bundled Studio JavaScript file read from the filesystem using node:fs/promises readFile, return HTTP 200, and set the content-type header to application/javascript.

*   GET /studio.css must serve the bundled Studio CSS file read from the filesystem using node:fs/promises readFile, return HTTP 200, and set the content-type header to text/css.

*   OPTIONS requests (e.g. to /bff) must return HTTP 204 with an empty body and the following CORS preflight headers: access-control-allow-origin: *, access-control-allow-methods: GET, HEAD, POST, OPTIONS, and access-control-allow-headers: Content-Type.

*   GET /adapter.js must return HTTP 404 (this route is no longer served).


*   Interface details: Type: Function
Name: startStudioServer
Location: packages/cli/src/studio-server.ts
Signature: startStudioServer(options: { handler: (request: Request) => Response | Promise<Response>, onListen: () => void, port: number }): StudioServer
Description: Starts an HTTP server that delegates incoming requests to the provided handler function. Calls onListen once the server is ready to accept connections. Returns a StudioServer object. On handler errors, responds with HTTP 500, sets access-control-allow-origin header to *, logs [Prisma Studio] and the error to console.error. For HEAD requests, returns the same status as the corresponding GET but with an empty body.

Type: Interface/Type
Name: StudioServer
Location: packages/cli/src/studio-server.ts
Description: Represents a running Studio HTTP server. Must be exported as a named type. Has a close() method that shuts down the server.
Signature: close(): void


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.