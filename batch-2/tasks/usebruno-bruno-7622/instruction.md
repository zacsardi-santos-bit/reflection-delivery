I need to add support for importing Swagger 2.

*   The swagger2ToBruno function must accept a Swagger 2.0 spec as a parsed object or as a YAML/JSON string, and must be a named export from packages/bruno-converters/src/openapi/swagger2-to-bruno.

*   The openApiToBruno function (default export from packages/bruno-converters/src/openapi/openapi-to-bruno) must detect Swagger 2.0 specs (identified by a top-level 'swagger: 2.0' field) and delegate to swagger2ToBruno; it must also accept YAML string input.

*   The returned collection must have: name (trimmed info.title, defaulting to 'Untitled Collection' for empty/whitespace/missing title), version equal to '1', a defined uid, an items array, and an environments array.

*   Environments must be built from the spec's host, basePath, and schemes: one environment per scheme named 'Environment 1', 'Environment 2', etc., with a 'baseUrl' variable. No host yields zero environments; missing or empty schemes default to 'https'. basePath without host yields a single environment whose baseUrl equals basePath.

*   Auth mapping: 'basic' securityDefinition → auth.mode 'basic' with username '{{username}}' and password '{{password}}'; 'apiKey in header' → mode 'apikey', placement 'header', and a corresponding header injected with value '{{apiKey}}'; 'apiKey in query' → mode 'apikey', placement 'queryparams', and a corresponding query param injected. Global security definitions set collection root auth; folder root auth is always mode 'inherit'.

*   OAuth2 flow mapping: 'implicit' → grantType 'implicit'; 'accessCode' → grantType 'authorization_code'; 'application' → grantType 'client_credentials'; 'password' → grantType 'password'. Operation with an explicit empty security array must produce auth.mode 'none'. Operation with no security (and no global security) must produce auth.mode 'inherit'.

*   Body mapping based on consumes (operation-level overrides global): 'application/json' or any content-type containing 'json' → body.mode 'json'; 'application/xml' or 'text/xml' → body.mode 'xml' with XML string containing a declaration and property tags (if schema example is a string, use it directly); formData params → body.mode 'formUrlEncoded' or 'multipartForm' (file-type fields have type 'file', others 'text'); '*/*' or 'application/octet-stream' → body.mode 'text'; body param with no schema → body.mode 'none'; no body params and empty params array → body.mode 'none'.

*   Parameter merging: path-item level parameters are merged into each operation; operation-level param with same name and 'in' value overrides the path-item version. Path params, query params, and header params are all supported.

*   Parameter value priority: param.example > param.default > first enum value > empty string. Values are stored as strings. enabled is false when the fallback is empty string, true otherwise. When enum is present without example/default, create one entry per enum value with the first enabled. When enum has a matching default, that entry's enabled is true and others false. collectionFormat 'multi' creates one entry per enum value; 'csv' joins with comma; 'pipes' joins with '|'; 'ssv' joins with ' '; 'tsv' joins with tab. Default for array+enum with no collectionFormat is csv. Object-type params expand to individual sub-properties.

*   Request name: use summary, fall back to operationId, fall back to method+path. Tag-based grouping uses a global usedNames set and appends '(METHOD)' for duplicates. Path-based grouping (options.groupBy = 'path') uses per-folder usedNames and creates nested folders for path segments. Untagged requests appear at root level in tag-based grouping.

*   Tag sanitization on each request's tags array: spaces and dots replaced with underscores; special characters like /, @, # removed or replaced; duplicates removed after sanitization; empty or missing tags produce an empty array. Sanitized tag names are also used for folder names in tag-based grouping.

*   Response examples: generate an examples array on the request item when responses have a schema or explicit examples object. Each example has uid, itemUid matching the parent request's uid, type 'http-request', name '{status} Response' (e.g. '200 Response'), statusText set to the HTTP status phrase, body type reflecting the produces content type, and a Content-Type response header. The example's request.url and request.method copy the parent request. Skip the 'default' response key. If no response has a schema or examples, examples must be undefined (not an empty array).

*   Circular schema references (including self-referencing and A→B→A chains) must not cause the converter to throw; it must return a valid collection with whatever body structure can be resolved without infinite recursion.


*   Interface details: Type: Function
Name: swagger2ToBruno
Location: packages/bruno-converters/src/openapi/swagger2-to-bruno.js
Signature: swagger2ToBruno(spec, options?) -> Collection
Description: Converts a Swagger 2.0 specification (as a parsed object or YAML/JSON string) into a Bruno collection object. The optional options object may include a `groupBy` field ('path' for path-based grouping; defaults to tag-based). Must be a named export.

Return shape:
{
  name: string,           // trimmed info.title; defaults to "Untitled Collection"
  version: '1',
  uid: string,
  items: Array<RequestItem | FolderItem>,
  environments: Array<{ name: string, variables: Array<{ name: string, value: string }> }>,
  root: { request: { auth: AuthObject } }
}

FolderItem shape:
{
  type: 'folder',
  name: string,
  items: Array<RequestItem | FolderItem>,
  root: { request: { auth: AuthObject }, meta: { name: string } }
}

RequestItem shape:
{
  type: 'http-request',
  name: string,
  uid: string,
  tags: string[],
  request: {
    url: string,
    method: string,
    auth: AuthObject,
    body: BodyObject,
    params: Array<ParamObject>,
    headers: Array<HeaderObject>
  },
  examples?: Array<ExampleObject>   // undefined if no response schema/examples
}

AuthObject:
{
  mode: 'basic' | 'apikey' | 'oauth2' | 'none' | 'inherit',
  basic?: { username: '{{username}}', password: '{{password}}' },
  apikey?: { key: string, placement: 'header' | 'queryparams' },
  oauth2?: {
    grantType: 'implicit' | 'authorization_code' | 'client_credentials' | 'password',
    authorizationUrl?: string,
    accessTokenUrl?: string,
    scope?: string
  }
}

BodyObject:
{
  mode: 'json' | 'xml' | 'text' | 'formUrlEncoded' | 'multipartForm' | 'none',
  json?: string,           // JSON-serialized body
  xml?: string,            // XML string (includes <?xml ...> declaration or raw string)
  formUrlEncoded?: Array<{ name: string, value: string, enabled: boolean }>,
  multipartForm?: Array<{ name: string, type: 'file' | 'text', value: string }>
}

ParamObject:
{
  name: string,
  type: 'query' | 'path',
  value: string,
  enabled: boolean,
  description?: string
}

HeaderObject:
{
  name: string,
  value: string,
  enabled?: boolean
}

ExampleObject:
{
  uid: string,
  itemUid: string,        // must equal parent RequestItem.uid
  type: 'http-request',
  name: string,           // e.g. '200 Response'
  request: {
    url: string,          // copied from parent
    method: string,       // copied from parent
    body: BodyObject
  },
  response: {
    status: number,
    statusText: string,   // HTTP status phrase
    body: { type: 'json' | 'xml', content: string },
    headers: Array<{ name: string, value: string }>
  }
}

Auth mapping rules:
- securityDefinitions type 'basic' → mode 'basic', username '{{username}}', password '{{password}}'
- securityDefinitions type 'apiKey', in 'header' → mode 'apikey', placement 'header'; injects header with value '{{apiKey}}'
- securityDefinitions type 'apiKey', in 'query' → mode 'apikey', placement 'queryparams'; injects query param
- oauth2 flow 'implicit' → grantType 'implicit'
- oauth2 flow 'accessCode' → grantType 'authorization_code'
- oauth2 flow 'application' → grantType 'client_credentials'
- oauth2 flow 'password' → grantType 'password'
- Operation with explicit security: [] → mode 'none'
- No security defined at operation or global → mode 'inherit'
- Global security → collection root.request.auth
- Folder root.request.auth.mode always 'inherit'

Body/consumes mapping rules:
- 'application/json' or any content-type containing 'json' → mode 'json'
- 'application/xml' or 'text/xml' → mode 'xml'; generates XML string with <?xml declaration and tags per schema properties
- If body schema example is a string (for XML content-type), use raw string directly as xml value
- 'application/x-www-form-urlencoded' or formData params → mode 'formUrlEncoded'
- 'multipart/form-data' → mode 'multipartForm'; file-type formData fields have type 'file', others 'text'
- '*/*' or 'application/octet-stream' → mode 'text'
- Operation-level consumes overrides global consumes
- Body parameter with no schema → mode 'none'

Parameter handling rules:
- Path-item level parameters are merged into all operations; operation-level param with same name+in overrides
- Value priority: param.example > param.default > first enum value > empty string
- enabled = false when value is empty string fallback; enabled = true when example/default/enum present
- Enum params without example or default: create one entry per enum value; first entry enabled = true (especially when required)
- When enum has a matching default: that entry's enabled = true, others enabled = false
- param.example takes priority over param.default
- All values stored as strings (numbers coerced)
- collectionFormat 'multi': one entry per enum value
- collectionFormat 'csv' (default): join enum values with ','
- collectionFormat 'pipes': join with '|'
- collectionFormat 'ssv': join with ' '
- collectionFormat 'tsv': join with '\t'
- Default collectionFormat for array+enum with no format specified: csv
- Object-type params expanded to individual sub-properties

Grouping rules:
- Default: tag-based; requests with tags go into folders named by sanitized tag; untagged at root
- options.groupBy = 'path': path-based; first path segment creates top-level folder; nested segments create nested folders
- Tag-based deduplication: global usedNames set; duplicate name → append '(METHOD)'
- Path-based deduplication: per-folder usedNames
- Fallback name when no summary and no operationId: method + path (e.g., 'get /items')

Tag sanitization:
- Spaces → underscores
- Dots → underscores
- Special characters (/, @, #, etc.) → removed or replaced
- Duplicates after sanitization removed
- Sanitized names used for folder names
- Empty tags array → request.tags = []
- Missing tags property → request.tags = []
- UTF-8/non-ASCII characters handled without throwing

Environment rules:
- One environment per scheme (https, http, etc.)
- Named 'Environment 1', 'Environment 2', etc.
- Variable: { name: 'baseUrl', value: '<scheme>://<host><basePath>' }
- No host → zero environments
- No schemes or empty schemes → default to 'https'
- basePath only (no host): single environment with value = basePath

Circular references: Must not throw; must return valid collection with whatever body can be generated without infinite recursion.

---

Type: Function
Name: openApiToBruno
Location: packages/bruno-converters/src/openapi/openapi-to-bruno.js
Signature: openApiToBruno(spec) -> Collection
Description: Entry point that routes API spec objects or YAML strings to the appropriate converter. When the spec has a 'swagger: "2.0"' field (or equivalent in YAML), it must delegate to swagger2ToBruno. Must be the default export.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.