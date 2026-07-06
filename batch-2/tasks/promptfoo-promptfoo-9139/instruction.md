I'm working on extending the QuiverAI provider to support image vectorization (converting raster images to SVG vector graphics) in addition to the existing text-to-SVG generation.

*   The default model name for QuiverAiProvider must be 'arrow-1.1' (replacing the previous default).

*   QuiverAiProvider must expose a public 'mode' property of type 'generation' | 'vectorize'. The constructor must accept an optional 'mode' parameter that defaults to 'generation'.

*   When mode is 'vectorize', id() must return 'quiverai:vectorize:{modelName}'. When mode is 'generation', id() must return 'quiverai:{modelName}'.

*   When mode is 'vectorize', toString() must return '[QuiverAI Vectorize Provider {modelName}]'. When mode is 'generation', toString() must return '[QuiverAI Provider {modelName}]'.

*   When mode is 'vectorize', getApiUrl() must return 'https://api.quiver.ai/v1/svgs/vectorizations'. When mode is 'generation', getApiUrl() must return 'https://api.quiver.ai/v1/svgs/generations'.

*   The non-streaming generation request body must include fields in this order: model, prompt, stream, then sampling parameters (temperature, max_output_tokens), then generation parameters (instructions). Specifically, temperature must appear before instructions in the serialized body.

*   String entries in the 'references' config array must be normalized to '{ url: string }' objects before being sent in the generation request body. Existing '{ url }' and '{ base64 }' objects must pass through unchanged.

*   The non-streaming generation callApi result must include 'metadata: { responseId: response.id, credits: response.credits }' when those values are present in the API response.

*   In vectorize mode, callApi must parse the prompt as an image reference using these rules: (1) an HTTPS URL string becomes '{ url: prompt }'; (2) a 'data:image/...;base64,...' data URL becomes '{ base64: <payload> }'; (3) a non-base64 data URL returns an error containing 'data URLs must be base64-encoded'; (4) a JSON string '{ url: ... }' or '{ base64: ... }' becomes that image object; (5) a JSON string with a nested 'image' key containing url/base64 uses that nested image; (6) an otherwise valid JSON object without url or base64 returns an error containing 'JSON image input must contain a non-empty `url` or `base64` string'; (7) a JSON with nested 'image' missing url/base64 returns an error containing 'nested `image` must contain a non-empty `url` or `base64` string'; (8) a raw base64 string becomes '{ base64: prompt }'; (9) an empty or whitespace-only prompt returns an error containing 'vectorize requires an image'.

*   In vectorize mode, if 'config.image' is set, it must override the prompt-derived image. If 'config.image' is malformed (lacks a non-empty 'url' or 'base64' string), callApi must return an error containing '`image` must contain a non-empty `url` or `base64` string' without calling the API.

*   In vectorize mode, the request body must include 'model', 'image', 'stream', and any vectorize-specific config keys (auto_crop, target_size). It must NOT include generation-only keys: 'instructions', 'references', or 'n'.

*   The streaming callApi result metadata must include '{ responseId, credits }' where responseId is the 'id' field from the first content event and credits is the sum of credits across all content events.

*   When streaming with n > 1, multiple SVG outputs must be ordered by their 'index' field from the SSE content events (not by arrival order) and joined with double newlines.

*   In streaming mode, a 429 response with 'code: weekly_limit_exceeded' in the response body must NOT be retried. The error must contain both 'Weekly limit exceeded' (the message) and 'weekly_limit_exceeded' (the code). A single API call must be made.

*   createQuiverAiProvider must support these path formats: 'quiverai:<model>' → generation mode, 'quiverai:chat:<model>' → generation mode (legacy alias), 'quiverai:generate:<model>' → generation mode (explicit), 'quiverai:vectorize:<model>' → vectorize mode. In all cases an empty model segment defaults to 'arrow-1.1'. The returned provider's 'mode' property must match the mode determined by the path prefix.

*   extractAndStoreBinaryData must detect when 'response.output' is a string containing a single-root SVG document (valid XML with a single 'svg' root element). When detected, it must store the SVG as a blob using MIME type 'image/svg+xml' and a context object containing 'location: "response.output"' and 'kind: "image"'. The original SVG text must be preserved in 'result.output' (not replaced with a blob URI). The blob's URI must be added to 'result.metadata.blobUris'.

*   extractAndStoreBinaryData must not store an SVG preview blob if the SVG's computed blob URI (based on SHA-256 of the UTF-8 content) is already present in 'response.metadata.blobUris'. In that case the original response must be returned unchanged.

*   extractAndStoreBinaryData must not store a blob when 'response.output' contains multiple root SVG elements (e.g., two '<svg>...' documents separated by whitespace). In that case the original response must be returned unchanged.

*   When the evaluator processes a provider response whose output is SVG text, the raw SVG text must be passed to any judge (such as an llm-rubric judge) as-is, not replaced with a blob URI. The stored evaluation result must contain 'response.metadata.blobUris' with a URI matching the pattern 'promptfoo://blob/{64-character lowercase hex hash}'.


*   Interface details: Type: Class
Name: QuiverAiProvider
Location: src/providers/quiverai.ts
Description: Provider for QuiverAI SVG generation and vectorization. Supports two modes: 'generation' (text → SVG, the default) and 'vectorize' (image → SVG). Exposes mode and modelName as public properties.
Signature: constructor(modelName: string, options?: { config?: QuiverAiProviderOptions; id?: string; env?: EnvOverrides; mode?: 'generation' | 'vectorize' })
Public property mode: 'generation' | 'vectorize'
Public property modelName: string
id() -> string
toString() -> string
getApiUrl() -> string
callApi(prompt: string, context?: CallApiContextParams) -> Promise<ProviderResponse>

Type: Function
Name: createQuiverAiProvider
Location: src/providers/quiverai.ts
Signature: createQuiverAiProvider(providerPath: string, providerOptions?: ProviderOptions, env?: EnvOverrides) -> ApiProvider
Description: Factory function that parses a provider path string and returns a QuiverAiProvider instance. Supports path formats: 'quiverai:<model>', 'quiverai:generate:<model>', 'quiverai:vectorize:<model>', 'quiverai:chat:<model>' (legacy alias for generation). Empty model segment defaults to 'arrow-1.1'.

Type: Function
Name: extractAndStoreBinaryData
Location: src/blobs/extractor.ts
Signature: extractAndStoreBinaryData(response: ProviderResponse | null | undefined, context?: BlobContext) -> Promise<ProviderResponse | null | undefined>
Description: Processes a provider response and externalizes binary content (images, audio, SVG) to blob storage. When the output is a valid single-root SVG string, it stores the SVG as a media blob while preserving the original SVG text in output and adding the blob URI to metadata.blobUris.

Type: Function
Name: sha256
Location: src/util/createHash.ts
Signature: sha256(buffer: Buffer) -> string
Description: Returns the SHA-256 hex digest of the given buffer. Used to compute blob URIs for SVG preview deduplication.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.