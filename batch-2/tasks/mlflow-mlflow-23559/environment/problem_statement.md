## Add MLflow AI Gateway as an Assistant Provider and Consolidate Streaming Infrastructure

### Description

The MLflow assistant currently supports a small set of external model providers (such as locally-run models and cloud-based coding assistants). Users who have already set up an MLflow AI Gateway endpoint on their MLflow server cannot use that gateway as the backing model for the assistant — they must configure a separate external provider. This is a gap: the gateway is right there in the same server, but it has no first-class integration with the assistant.

Additionally, the streaming logic that handles real-time responses from model providers is currently duplicated across provider implementations. This makes it harder to add new compatible providers and means improvements (such as filtering out internal reasoning blocks that some models embed inline in their responses) must be replicated everywhere.

There is also a security concern with the current approach to model discovery: when the frontend asks the backend to enumerate available models for a provider, the API key is sent as a URL query parameter. Query parameters appear in server access logs, browser history, and HTTP referrer headers — all of which are common sources of credential leaks.

### Expected Behavior

- Users should be able to select MLflow AI Gateway as a provider option and pick an existing gateway endpoint as their assistant model. The gateway uses MLflow's own server for routing, so no separate base URL needs to be configured.
- A shared streaming engine should handle the OpenAI-compatible chat protocol, including: filtering inline reasoning blocks from responses before they reach the user, accumulating multi-part tool call payloads correctly, and parsing the standard event-stream format.
- Providers that do not support programmatic model listing (such as the MLflow Gateway, which surfaces its endpoints through existing UI APIs) must clearly indicate this rather than silently returning success.
- API keys used for model listing must be transmitted via a dedicated request header, not as URL query parameters.
- Provider configurations must support storing an API key so that authenticated backends can be used without manual key entry on each request.

### Why This Matters

Users running MLflow with the AI Gateway enabled should get a seamless experience connecting the assistant to their gateway — rather than needing a separate provider. Moving credentials out of URLs prevents them from leaking into logs and browser history. Centralizing streaming logic reduces duplication and makes it easier to add future providers.
