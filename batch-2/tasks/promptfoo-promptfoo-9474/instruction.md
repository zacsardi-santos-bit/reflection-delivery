I'm working on the promptfoo codebase and want to add a client identification header to all outgoing OpenAI API requests.

*   A constant named DEFAULT_OPENAI_ORIGINATOR must be exported from src/providers/openai/index.ts with the string value 'promptfoo'.

*   A constant named OPENAI_ORIGINATOR_HEADER must be exported from src/providers/openai/index.ts with the string value 'X-OpenAI-Originator'.

*   The OpenAiGenericProvider.getOpenAiRequestHeaders() method must include { 'X-OpenAI-Originator': 'promptfoo' } in its returned headers when the provider is configured with the default OpenAI API endpoint (no custom apiBaseUrl).

*   The OpenAiGenericProvider.getOpenAiRequestHeaders(overrides) method must accept an optional overrides parameter (Record<string, string>) whose values take precedence over defaults — for example, passing { 'X-OpenAI-Originator': 'custom-originator' } must result in that custom value being present in the returned headers.

*   When OpenAiGenericProvider is initialized with a custom apiBaseUrl, getOpenAiRequestHeaders() must NOT include the 'X-OpenAI-Originator' header by default. Explicit overrides must still apply.

*   All OpenAI chat completion API requests must include the 'X-OpenAI-Originator': 'promptfoo' header in their HTTP request headers.

*   All OpenAI text completion API requests must include the 'X-OpenAI-Originator': 'promptfoo' header in their HTTP request headers.

*   All OpenAI embedding API requests must include the 'X-OpenAI-Originator': 'promptfoo' header in their HTTP request headers.

*   All OpenAI image generation API requests must include the 'X-OpenAI-Originator': 'promptfoo' header in their HTTP request headers.

*   All OpenAI moderation API requests must include the 'X-OpenAI-Originator': 'promptfoo' header in their HTTP request headers.

*   All OpenAI responses API requests, transcription API requests, and video API requests must include the 'X-OpenAI-Originator': 'promptfoo' header in their HTTP request headers.

*   OpenAI Realtime provider WebSocket connections must include the 'X-OpenAI-Originator': 'promptfoo' header in the WebSocket constructor options headers.

*   The OpenAI Assistants provider must pass 'X-OpenAI-Originator': 'promptfoo' as a default header when constructing the OpenAI SDK client (via the defaultHeaders option).

*   The OpenAI ChatKit provider must include 'X-OpenAI-Originator': 'promptfoo' in the headers object of its generated HTML fetch configuration.


*   Interface details: Type: Constant
Name: DEFAULT_OPENAI_ORIGINATOR
Location: src/providers/openai/index.ts
Signature: DEFAULT_OPENAI_ORIGINATOR: string
Description: The default value for the originator identification header sent with OpenAI API requests. Value is 'promptfoo'.

Type: Constant
Name: OPENAI_ORIGINATOR_HEADER
Location: src/providers/openai/index.ts
Signature: OPENAI_ORIGINATOR_HEADER: string
Description: The HTTP header name used to identify the originating client. Value is 'X-OpenAI-Originator'.

Type: Method
Name: getOpenAiRequestHeaders
Location: src/providers/openai/index.ts (on OpenAiGenericProvider class)
Signature: getOpenAiRequestHeaders(overrides?: Record<string, string>) -> Record<string, string>
Description: Returns the default HTTP headers to include with OpenAI API requests. When the provider uses the default OpenAI API endpoint (no custom apiBaseUrl), includes 'X-OpenAI-Originator': 'promptfoo' in the returned headers. When a custom apiBaseUrl is configured, does NOT include the X-OpenAI-Originator header by default. In both cases, any values passed via the overrides parameter take precedence over defaults, allowing the originator header to be explicitly set even for custom endpoints.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.