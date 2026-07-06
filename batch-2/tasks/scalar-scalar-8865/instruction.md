I'm working on an API client and I need to add support for custom response body decoders via a plugin system.

*   The decodeBuffer function must be async (return a Promise). It must accept an optional third argument of type ResponseBodyHandler. When that handler has a decode function, decodeBuffer must call handler.decode(buffer, contentType) — passing both the ArrayBuffer and the full content type string as a second argument — and return its result (supporting both synchronous and async decode return values). When the handler has no decode function, decodeBuffer must fall back to default decoding behavior.

*   The sendRequest function's argument object must include a plugins field accepting an array of ClientPlugin objects. This field must be included in the arguments when calling sendRequest.

*   When sendRequest receives a response with no content-type header, it must resolve the content type to DEFAULT_RESPONSE_CONTENT_TYPE ('text/plain;charset=UTF-8') before resolving a plugin decoder and before calling decodeBuffer. When a matching plugin decoder is found, its decode function must receive the ArrayBuffer and the resolved content type string (e.g., 'text/plain;charset=UTF-8').

*   The processResponseBody function must default the returned mimeType.essence to 'text/plain' when no content-type header is present in the headers array.

*   A new resolveResponseBodyHandler function must be created at packages/api-client/src/v2/blocks/response-block/helpers/resolve-response-body-handler.ts. It accepts a MIME type string and an array of ClientPlugin objects, and returns the first matching ResponseBodyHandler or undefined. Matching is case-insensitive. Patterns in a handler's mimeTypes array support wildcards (e.g., 'application/vnd.*+json' matches 'application/vnd.api+json'). Plugins without a responseBody property are skipped. A single handler may declare multiple MIME types in its mimeTypes array.

*   A new resolveResponseContentType function must be created at packages/api-client/src/v2/blocks/response-block/helpers/resolve-response-content-type.ts. It accepts a string, undefined, or null. It returns the input unchanged when it is a non-null, non-undefined string, and returns DEFAULT_RESPONSE_CONTENT_TYPE when the input is undefined or null.

*   A new resolveResponseMimeType function must be created in the same file as resolveResponseContentType. It accepts a string, undefined, or null, and returns a parsed MIME type object with an essence property. When the input is undefined or null, the returned object's essence property must equal 'text/plain'.

*   A DEFAULT_RESPONSE_CONTENT_TYPE constant must be exported from packages/api-client/src/v2/blocks/response-block/helpers/resolve-response-content-type.ts with the exact value 'text/plain;charset=UTF-8' (not just 'text/plain'). This value is used as the fallback when a response has no content-type header.

*   The ResponseBodyHandler type (from @scalar/oas-utils/helpers) must include: mimeTypes (string[]), an optional decode function with signature (buf: ArrayBuffer, contentType?: string) => string | Blob | Promise<string | Blob>, and an optional language string. The ClientPlugin type must include an optional responseBody array of ResponseBodyHandler objects.


*   Interface details: Type: Function
Name: decodeBuffer
Location: packages/api-client/src/v2/blocks/operation-block/helpers/decode-buffer.ts
Signature: decodeBuffer(buffer: ArrayBuffer, contentType: string, pluginHandler?: ResponseBodyHandler): Promise<string | Blob>
Description: Decodes an ArrayBuffer into a string or Blob based on the content type. When a ResponseBodyHandler with a decode function is provided, calls handler.decode(buffer, contentType) — passing both the buffer and the full content type string — and returns its result. Falls back to default text/binary decoding when no handler is provided or the handler lacks a decode function. Must be async (return a Promise).

Type: Function
Name: sendRequest
Location: packages/api-client/src/v2/blocks/operation-block/helpers/send-request.ts
Signature: sendRequest({ isUsingProxy, request, plugins }: { isUsingProxy: boolean, request: Request, plugins: ClientPlugin[] }): Promise<[Error | null, result?]>
Description: Sends an HTTP request and returns the response. Now accepts a plugins array in its argument object. When the response has no content-type header, resolves the content type to DEFAULT_RESPONSE_CONTENT_TYPE ('text/plain;charset=UTF-8') to search for a matching plugin handler. The plugin decode function is called with the ArrayBuffer and the resolved content type string (e.g., 'text/plain;charset=UTF-8').

Type: Function
Name: resolveResponseBodyHandler
Location: packages/api-client/src/v2/blocks/response-block/helpers/resolve-response-body-handler.ts
Signature: resolveResponseBodyHandler(mimeType: string, plugins: ClientPlugin[]): ResponseBodyHandler | undefined
Description: Searches through an array of ClientPlugin objects and returns the first ResponseBodyHandler whose mimeTypes array matches the given MIME type string. Matching is case-insensitive and supports wildcard glob patterns (e.g., 'application/vnd.*+json' matches 'application/vnd.api+json'). Returns undefined when no match is found or when plugins is empty. Skips plugins that lack a responseBody property.

Type: Function
Name: resolveResponseContentType
Location: packages/api-client/src/v2/blocks/response-block/helpers/resolve-response-content-type.ts
Signature: resolveResponseContentType(contentType: string | undefined | null): string
Description: Returns the provided content type string when it is non-null and non-undefined. Returns DEFAULT_RESPONSE_CONTENT_TYPE ('text/plain;charset=UTF-8') when the input is null or undefined.

Type: Function
Name: resolveResponseMimeType
Location: packages/api-client/src/v2/blocks/response-block/helpers/resolve-response-content-type.ts
Signature: resolveResponseMimeType(contentType: string | undefined | null): { essence: string }
Description: Parses the provided content type string and returns an object with an essence property. When contentType is undefined or null, returns an object with essence equal to 'text/plain'.

Type: Constant
Name: DEFAULT_RESPONSE_CONTENT_TYPE
Location: packages/api-client/src/v2/blocks/response-block/helpers/resolve-response-content-type.ts
Signature: DEFAULT_RESPONSE_CONTENT_TYPE: string = 'text/plain;charset=UTF-8'
Description: The default content type string used when a response has no content-type header. Value is exactly 'text/plain;charset=UTF-8' (matching the browser's implicit default when no Content-Type header is present in the response).

Type: Interface
Name: ResponseBodyHandler
Location: packages/oas-utils/src/helpers (exported from @scalar/oas-utils/helpers)
Description: Describes a plugin handler for a specific set of MIME types. Has: mimeTypes (string[]) — the MIME types this handler handles; decode (optional) — a function taking (buf: ArrayBuffer, contentType?: string) and returning string, Blob, or Promise<string | Blob>; language (optional string) — a language identifier for syntax highlighting.

Type: Interface
Name: ClientPlugin
Location: packages/oas-utils/src/helpers (exported from @scalar/oas-utils/helpers)
Description: Describes a client plugin. Has: responseBody (optional ResponseBodyHandler[]) — array of response body handlers; hooks (optional object with beforeRequest function) — lifecycle hooks.

Type: Function
Name: processResponseBody
Location: packages/api-client/src/v2/blocks/response-block/helpers/process-response-body.ts
Signature: processResponseBody(props: { data: unknown, headers: { name: string, value: string }[] }): { mimeType: { essence: string } }
Description: Processes a response body and extracts MIME type and other metadata from the response headers. Modified behavior: when no content-type header is present in the headers array, the returned mimeType.essence must equal 'text/plain'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.