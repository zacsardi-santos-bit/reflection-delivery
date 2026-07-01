I'm working on the Shopify CLI theme development server and I need to make several improvements to how it handles HTTP responses and errors.

First, the server currently imports fetch and Response objects from a third-party HTTP package. I want to migrate everything to use the native, built-in fetch API instead, since modern Node.js supports it natively. This should remove the dependency on the external HTTP package across all the theme environment utilities.

Second, the response-patching function that modifies HTML responses from the storefront renderer currently mutates the outgoing server response in place (taking an event object as a parameter). I'd like to change it so it returns a proper Response object instead, making the data flow cleaner and the function easier to use and test.

Third, I need better error handling in the HTML rendering pipeline. Right now, if the renderer returns a 4xx error (for routes it can't handle like certain account or app-specific routes), the server just fails. Instead, it should automatically fall back to proxying the request directly to the store. If the proxy succeeds (responds with a status below 400), return that response. If the proxy also fails, return the original renderer's response.

If the renderer throws a network error entirely, the server should return an HTML error page with a 502 status and the hot-reload script injected, so the developer can see the error and watch for live updates.

Similarly, if there are pending file upload errors in the local theme, the server should skip rendering entirely and return an error page listing the failed files and their error messages — again with the hot-reload script included.

Finally, I'd like to introduce two reusable utility functions for working with fetch errors: one that creates a structured fetch error object from either a Response or a thrown Error, and one that extracts displayable information (headline, status, status text, request ID, URL) from such an error object. The latter should default to a 502 Bad Gateway status for plain errors.
