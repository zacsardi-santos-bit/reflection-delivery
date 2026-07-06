## Description

The AI gateway's endpoint usage modal currently only shows static code examples (in different languages) for how to call a gateway endpoint. There's no way to actually test the endpoint from the UI — users have to copy the code and run it externally. Additionally, the backend has a compatibility issue where outgoing HTTP requests to AI providers can fail if the provider responds with Brotli compression, which the HTTP client library cannot decode without an optional dependency.

## Expected Behavior

- The usage modal should default to an interactive "Try it" view where users can send a real request directly from the browser, see the response immediately, and inspect any errors — all without leaving the UI.
- The "Try it" view should show a request body editor, a "Send request" button, and a dedicated response area.
- Users should still be able to switch to code-example views (cURL, Python) via view mode selectors.
- Switching between API types (unified vs. passthrough) or providers should reset the request body to an appropriate default and clear any previous response or error.
- Closing and reopening the modal should clear the previous response and error state.
- A "Reset example" button should restore the default request body and clear any displayed response or error.
- On the backend, when making HTTP requests to AI provider APIs, the server should explicitly advertise only compression formats it can decode, preventing failures caused by unsupported compression in responses.

## Why This Matters

Without an interactive test panel, developers must copy and paste code examples into their terminal just to verify an endpoint works, which is slow and error-prone. The compression issue on the backend can cause silent or confusing failures when certain providers are used, and fixing it improves reliability across all gateway providers.
