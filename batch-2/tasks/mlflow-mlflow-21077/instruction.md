I'm working on improving the AI gateway's endpoint usage modal in our application.

*   The EndpointUsageModal component must require a React Query context provider to function correctly.

*   The EndpointUsageModal must default to showing the 'Try it' view in the Unified APIs tab, displaying a 'Send request' button, a 'Request' section, and a 'Response' section.

*   The EndpointUsageModal view mode selector must include 'Try it', 'cURL', and 'Python' options as radio buttons, with 'Try it' as the default.

*   When 'cURL' is selected in the Unified APIs tab, only one curl code block is rendered (for the currently active variant), not multiple.

*   When 'Send request' is clicked in the Try it panel, a POST request must be sent to '{baseUrl}/gateway/{endpointName}/mlflow/invocations' with Content-Type: application/json.

*   When the 'OpenAI Chat Completions' variant is selected, the request must be sent to '{baseUrl}/gateway/mlflow/v1/chat/completions', and the default request body must include 'model', 'messages', and the endpoint name.

*   Authentication headers must be included in the outgoing Try it request when they are available.

*   If the request body is not valid JSON when 'Send request' is clicked, no fetch call must be made and the error message 'Invalid JSON in request body' must be displayed.

*   If the server responds with an HTTP error, the error's message text must be shown (e.g., 'Internal server error' for a 500 response) and the response body text must be displayed in the response area.

*   If the network request fails with a non-HTTP error, the error's message text must be displayed.

*   In the Passthrough APIs tab's Try it panel, the default request body for OpenAI must contain 'model'; for Anthropic it must contain 'max_tokens' and 'messages'; for Google Gemini it must contain 'contents'.

*   Switching between Unified APIs and Passthrough APIs tabs must clear any existing response and error state.

*   Switching the provider selection in the Passthrough APIs tab must clear any existing response and error state.

*   When the modal is closed and then reopened, all response and error state must be cleared.

*   A 'Reset example' button must restore the default request body for the current tab/variant and clear any existing response and error state.

*   The useTryIt hook must accept an options object with a 'tryItRequestUrl' string property.

*   The useTryIt hook must return: data (string or undefined), error ({ message: string, responseBody?: string } or undefined), isLoading (boolean), sendRequest (function accepting a string body), and reset (function).

*   On a successful request, useTryIt must set data to the response body pretty-printed as JSON (via JSON.stringify with 2-space indent).

*   When useTryIt encounters an HTTP error that carries a response object (such as a NetworkRequestError or its subclasses like GenericNetworkRequestError), error.message must equal the error's own message (e.g., 'A network error occurred.' for GenericNetworkRequestError with status 400) and error.responseBody must be the response body text pretty-printed as JSON where possible.

*   When useTryIt encounters a generic non-HTTP Error (one that does not carry a response body), error.message must equal the error's message and error.responseBody must be undefined.

*   When useTryIt's sendRequest is called with invalid JSON, error.message must be 'Invalid JSON in request body' and the fetch function must not be called.

*   Calling useTryIt's reset() method must clear both data and error to undefined.

*   The SUPPORTED_ACCEPT_ENCODING constant must be exported from the gateway provider utils module with the value 'gzip, deflate, identity'.

*   The _aiohttp_post function must be exported from the gateway provider utils module.

*   When _aiohttp_post creates the aiohttp.ClientSession, the session headers must include Accept-Encoding set to the value of SUPPORTED_ACCEPT_ENCODING.

*   Any Accept-Encoding key (regardless of casing) present in the input headers passed to _aiohttp_post must be removed before setting Accept-Encoding to SUPPORTED_ACCEPT_ENCODING.

*   Authorization and other auth headers passed to _aiohttp_post must be forwarded to the aiohttp.ClientSession.


*   Interface details: Type: Hook
Name: useTryIt
Location: mlflow/server/js/src/gateway/hooks/useTryIt.ts
Signature: useTryIt({ tryItRequestUrl: string }) -> { data: string | undefined, error: { message: string, responseBody?: string } | undefined, isLoading: boolean, sendRequest: (body: string) => void, reset: () => void }
Description: React hook that manages sending a POST request to the given URL and tracking data, error, and loading state. Uses fetchOrFail internally. The sendRequest function takes the raw request body string. If the body is not valid JSON, sets error.message to 'Invalid JSON in request body' and does not call fetchOrFail. On success, sets data to the response body pretty-printed as JSON (JSON.stringify(parsed, null, 2)). When the request fails with a NetworkRequestError (or subclass such as GenericNetworkRequestError), sets error.message to the error's own message and error.responseBody to the response body text pretty-printed. When the request fails with a non-NetworkRequestError, sets error.message to the error's message and leaves error.responseBody undefined. reset() clears both data and error to undefined.

Type: Constant
Name: SUPPORTED_ACCEPT_ENCODING
Location: mlflow/gateway/providers/utils.py
Description: String constant exported from the module. Value must be "gzip, deflate, identity".

Type: Function
Name: _aiohttp_post
Location: mlflow/gateway/providers/utils.py
Signature: _aiohttp_post(headers: dict[str, str], base_url: str, path: str, payload: dict[str, Any]) -> async context manager yielding the response
Description: Async context manager exported from the module that creates an aiohttp.ClientSession and performs a POST. The ClientSession must be created with headers that include Accept-Encoding set to SUPPORTED_ACCEPT_ENCODING. Any incoming Accept-Encoding key (regardless of casing) in the provided headers must be removed before setting the single Accept-Encoding value. Authorization and other auth headers from the input headers dict must be preserved.

Type: Component
Name: EndpointUsageModal
Location: mlflow/server/js/src/gateway/components/endpoints/EndpointUsageModal.tsx
Description: React component requiring QueryClientProvider context. Must default to showing the "Try it" panel in the Unified APIs tab, displaying a "Send request" button, a "Request" label, and a "Response" label. The view mode selector must include "Try it", "cURL", and "Python" radio button options. When cURL is selected, only one curl code block is shown (the currently selected variant). When the modal transitions from open to closed and back to open, all response and error state must be cleared.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.