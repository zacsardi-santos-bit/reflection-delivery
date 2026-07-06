Enhance the 'new-window' event in Electron to include referrer information. This will allow applications to forward the referrer context when handling new window requests, ensuring the proper Referer HTTP header is sent to the server.

*   Update the 'new-window' event emitted by `webContents`:
    *   Add a 'referrer' object as the 7th argument in the event callback.
    *   Ensure the callback signature is `(event, url, frameName, disposition, options, additionalFeatures, referrer)`.

*   Implement the 'referrer' object with the following properties:
    *   `url` (string): Set to the URL of the page that initiated the new window request.
    *   `policy` (string): Set to the applicable referrer policy. For standard HTTP pages opened via `target=_blank` links, use 'no-referrer-when-downgrade'.

*   Ensure that when a new window is opened via a link with `target=_blank`:
    *   The HTTP Referer header on subsequent requests from the new window is set to the originating page's URL.

*   Modify the following files:
    *   `lib/browser/api/web-contents.js`: Emit the 'new-window' event with the updated signature.
    *   `docs/api/structures/referrer.md`: Document the structure of the 'referrer' object.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.