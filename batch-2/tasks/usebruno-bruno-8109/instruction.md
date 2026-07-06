I'm working on Bruno's API spec panel, which lets users try out endpoints from a Swagger or OpenAPI document.

*   The serializeBody function must return undefined when called with null or undefined.

*   The serializeBody function must return string inputs unchanged.

*   The serializeBody function must convert URLSearchParams inputs to a URL-encoded query string (e.g. 'a=1&b=2').

*   The serializeBody function must throw a TypeError for FormData inputs. The error message must match /Multipart form data/ and /Create a Bruno request/. The error must have a .code property equal to 'UNSUPPORTED_BODY_TYPE' and a .bodyType property equal to 'FormData'.

*   The serializeBody function must throw a TypeError for Blob inputs. The error message must match /Binary file upload/. The error must have .code equal to 'UNSUPPORTED_BODY_TYPE' and .bodyType equal to 'Blob'.

*   The serializeBody function must throw a TypeError for File inputs. The error message must match /File upload/. The error must have .code equal to 'UNSUPPORTED_BODY_TYPE' and .bodyType equal to 'File'.

*   The serializeBody function must throw a TypeError for ArrayBuffer inputs. The error message must match /Binary data/. The error must have .code equal to 'UNSUPPORTED_BODY_TYPE' and .bodyType equal to 'ArrayBuffer'.

*   The serializeBody function must throw a TypeError for TypedArray inputs (e.g. Uint8Array). The error message must match /Binary data/. The error must have .code equal to 'UNSUPPORTED_BODY_TYPE' and .bodyType equal to the typed array constructor name (e.g. 'Uint8Array').

*   UNSUPPORTED_BODY_TYPE_CODE must be exported as the string 'UNSUPPORTED_BODY_TYPE'.

*   UNSUPPORTED_BODY_MESSAGE(bodyTypeName) must return a string that matches /isn't supported in Bruno yet/ and /JSON, URL-encoded forms, plain text/.

*   The proxySwaggerFetch function must return { error: true, code: 'INVALID_REQUEST' } without making any HTTP request when called with no argument or when the payload has no url property.

*   The proxySwaggerFetch function must return { status, statusText, headers, bodyBase64 } for all successful HTTP responses (both 2xx and non-2xx status codes). The bodyBase64 field must be the response body encoded as a base64 string. The result must not have an 'error' field.

*   The proxySwaggerFetch function must return { error: true, code, message } when the underlying HTTP request throws a network or TLS error, where code and message are taken from the thrown error object.

*   When making the underlying HTTP request, proxySwaggerFetch must pass url, method, headers, and data (from the body field) to axios, and must set responseType to 'arraybuffer' and validateStatus to a function that returns true for all status codes (including 599).

*   The headers field in the success response must be a plain JavaScript object. If the axios response headers object has a toJSON() method, proxySwaggerFetch must call it and use the plain-object result, ensuring the returned headers object does not have a toJSON property.


*   Interface details: Type: Function
Name: serializeBody
Location: packages/bruno-app/src/components/ApiSpecPanel/Renderers/Swagger/serializeBody.js
Signature: serializeBody(body) -> undefined | string
Description: Serializes a request body for use in a Swagger "try it out" request. Returns undefined for null/undefined input. Returns string input as-is. Converts URLSearchParams to a query string (e.g. "a=1&b=2"). Throws a TypeError for unsupported body types (FormData, Blob, File, ArrayBuffer, TypedArray). The thrown TypeError must have a .code property equal to UNSUPPORTED_BODY_TYPE_CODE and a .bodyType property containing the constructor name of the unsupported type.

Type: Constant
Name: UNSUPPORTED_BODY_TYPE_CODE
Location: packages/bruno-app/src/components/ApiSpecPanel/Renderers/Swagger/serializeBody.js
Signature: UNSUPPORTED_BODY_TYPE_CODE: string
Description: Exported string constant with value 'UNSUPPORTED_BODY_TYPE'. Used as the .code property on TypeErrors thrown by serializeBody for unsupported body types.

Type: Function
Name: UNSUPPORTED_BODY_MESSAGE
Location: packages/bruno-app/src/components/ApiSpecPanel/Renderers/Swagger/serializeBody.js
Signature: UNSUPPORTED_BODY_MESSAGE(bodyTypeName: string) -> string
Description: Returns a human-readable error message for an unsupported body type. The message must include the phrase "isn't supported in Bruno yet" and list supported alternatives including "JSON, URL-encoded forms, plain text".

Type: Function
Name: proxySwaggerFetch
Location: packages/bruno-electron/src/ipc/swagger-fetch.js
Signature: proxySwaggerFetch(payload?) -> Promise<object>
Description: Proxies an HTTP request for the Swagger "try it out" panel through the Electron main process. Accepts a payload object with fields: url (string), method (string), headers (object), body (string or undefined). Returns a response object or an error object.

  Success shape (both 2xx and non-2xx HTTP statuses):
    { status: number, statusText: string, headers: object, bodyBase64: string }
  - bodyBase64 is the response body as a base64-encoded string (decoded from a Buffer).
  - headers must be a plain object — if the axios response headers object has a toJSON() method (e.g. AxiosHeaders in axios v1), it must be called and the plain-object result used.
  - The result object must NOT have an 'error' field for HTTP-level responses.

  Error shape (network/TLS failures):
    { error: true, code: string, message: string }
  - code and message come from the caught error object.

  Validation failure shape (missing payload or missing url):
    { error: true, code: 'INVALID_REQUEST' }
  - No HTTP request is made in this case.

  Axios call requirements:
  - Must set responseType to 'arraybuffer'.
  - Must set validateStatus to a function that always returns true (accepts all HTTP status codes).
  - Must forward url, method, and headers from the payload.
  - Must forward body as the 'data' field to axios.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.