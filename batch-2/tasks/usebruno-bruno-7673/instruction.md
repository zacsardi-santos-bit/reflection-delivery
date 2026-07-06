I'm working on Bruno, an API testing tool, and I need to add a proper header list API to both the request and response objects that are available in pre-request scripts and test scripts.

*   The HeaderList class must extend ReadOnlyPropertyList (which itself extends PropertyList). The static method ReadOnlyPropertyList.isPropertyList() must return true for HeaderList instances.

*   BrunoRequest must expose a headerList property that returns a mutable HeaderList instance backed by the request's headers. The headerList must dynamically reflect external mutations made via BrunoRequest's own header methods (setHeader, deleteHeader, setHeaders). BrunoRequest must also expose a headers property that returns the raw headers object directly.

*   BrunoResponse must expose a headerList property that returns a read-only HeaderList instance backed by the response's headers. BrunoResponse must also expose a headers property that returns the raw headers object. All write methods on the response headerList (append, delete, clear, set, populate, repopulate, assimilate) must throw an error whose message contains 'read-only'.

*   The headerList read methods must behave as follows: get(key) returns the header value string or undefined; one(key) returns the full {key, value} object or undefined; all() returns a cloned array of header objects in insertion order; idx(n) returns the header at position n or undefined; count() returns the total number of headers; indexOf(item) returns the 0-based index of the first matching header or -1.

*   The headerList search methods must behave as follows: has(key) returns true if a header with that key exists; has(key, value) returns true only if both key and value match; has({key}) returns true if a header with that key property exists; find(fn, ctx?) returns the first header matching the predicate; filter(fn, ctx?) returns all matching headers as an array.

*   The headerList iteration methods must behave as follows: forEach(fn, ctx?) iterates over all headers; map(fn, ctx?) transforms headers and returns an array; reduce(fn, acc?, ctx?) accumulates over headers, supporting both with and without initial value.

*   The headerList transform methods must behave as follows: toObject(excludeDisabled?, caseSensitive?, multiValue?, skipFalsyKeys?) returns a plain key-value object with optional filtering of disabled headers, optional lowercase keys, optional first-value-wins on duplicate keys, and optional skipping of falsy keys; toString() returns headers in HTTP wire format 'Key: value\n' per header, skipping disabled headers; toJSON() returns the same as all().

*   All key-based lookups (get, one, has, indexOf, delete by string) must be case-insensitive. When set() replaces a header with a different casing of the key, the old key must be removed and the new casing used; the header must not appear in the deletion-tracking list since it was re-added.

*   The headerList write method append() must accept: an object {key, value}; a 'Key: Value' string; or two arguments (name, value). It must silently ignore null, undefined, objects without a key property, and strings without a colon.

*   The headerList write method set() must accept: an object {key, value}; or two arguments (name, value). It must return true when adding a new header, false when replacing an existing one, and null for null, undefined, or objects missing a key property.

*   The headerList write method delete() must accept: a key string; a predicate function with optional context; or a header object {key, value}. It must track each removed key in rawReq.__headersToDelete. It must also remove entries from rawReq.disabledHeaders when the removed header is disabled. It must be a no-op for non-existent keys and for null or undefined input.

*   The headerList write method clear() must remove all headers (both enabled and disabled), clear rawReq.disabledHeaders, and track all removed enabled keys in rawReq.__headersToDelete.

*   The headerList write method populate(items) must accept an array of {key, value} objects or a multi-line header string (LF or CRLF). It must add headers whose keys do not already exist, and skip headers with keys that are already present. It must be a no-op for null or non-array/non-string inputs.

*   The headerList write method repopulate(items) must clear all existing headers and then populate with the provided items. Headers that are re-added must not appear in rawReq.__headersToDelete.

*   The headerList write method assimilate(source, prune?) must accept an array of {key, value} objects or another PropertyList as source. Without prune, it merges new items from source without removing existing headers. With prune=true, it removes all headers not present in source (including disabled headers) and adds/updates to match the source.

*   The headerList must include disabled headers (sourced from rawReq.disabledHeaders where each entry has name and value properties) in all() and count(). Disabled headers must appear with a disabled: true property in all(). Enabled headers with the same key take precedence over disabled ones in get(), one(), and toObject().

*   The translateBruToPostman function must translate req.headerList method calls to their pm.request.headers equivalents: get→get, has→has, all→all, filter→filter, one→one, find→find, toObject→toObject, clear→clear, append→add, delete→remove, set→upsert. Standalone req.headerList (without a method call) must translate to pm.request.headers.

*   The translateBruToPostman function must translate res.headerList method calls to their pm.response.headers equivalents: get→get, has→has, all→all, filter→filter, one→one, find→find, toObject→toObject. Standalone res.headerList must translate to pm.response.headers.

*   The translateCode function (Postman-to-Bruno) must translate pm.request.headers method calls to req.headerList equivalents: get→get, has→has, all→all, each→forEach, filter→filter, count→count, clear→clear, toObject→toObject.

*   The translateCode function (Postman-to-Bruno) must translate pm.response.headers method calls to res.headerList equivalents: has→has, all→all, each→forEach, filter→filter, count→count, toObject→toObject.

*   The legacy Postman tests syntax translation must produce res.headerList.has(...) (not res.getHeaders().has(...)) when translating header existence checks from legacy tests syntax.


*   Interface details: Type: Class
Name: HeaderList
Location: packages/bruno-js/src/header-list.js
Description: A mutable (for requests) or read-only (for responses) list of HTTP headers that extends ReadOnlyPropertyList. Exposes read, search, iteration, transform, and write methods. All key-based lookups are case-insensitive.
Signature:
  get(key: string) -> string|undefined
  one(key: string) -> {key, value}|undefined
  all() -> Array<{key, value}|{key, value, disabled}>
  idx(n: number) -> {key, value}|undefined
  count() -> number
  indexOf(item: string|{key, value}) -> number
  has(key: string|{key}, value?: string) -> boolean
  find(fn: function, ctx?: object) -> {key, value}|undefined
  filter(fn: function, ctx?: object) -> Array<{key, value}>
  forEach(fn: function, ctx?: object) -> void
  map(fn: function, ctx?: object) -> Array<any>
  reduce(fn: function, acc?: any, ctx?: object) -> any
  toObject(excludeDisabled?: boolean, caseSensitive?: boolean, multiValue?: boolean, skipFalsyKeys?: boolean) -> object
  toString() -> string
  toJSON() -> Array<{key, value}>
  append(item: {key, value}|string, value?: string) -> void  [write methods throw on read-only instance]
  set(item: {key, value}|string, value?: string) -> boolean|null
  delete(item: string|function|{key, value}, ctx?: object) -> void
  clear() -> void
  populate(items: Array<{key, value}>|string) -> void
  repopulate(items: Array<{key, value}>) -> void
  assimilate(source: Array<{key, value}>|PropertyList, prune?: boolean) -> void

Type: Class
Name: ReadOnlyPropertyList
Location: packages/bruno-js/src/readonly-property-list.js
Description: Base class extended by HeaderList. Provides the isPropertyList static method.
Signature:
  static isPropertyList(instance: any) -> boolean

Type: Class
Name: PropertyList
Location: packages/bruno-js/src/property-list.js
Description: Base class extended by ReadOnlyPropertyList. Inherited by HeaderList.

Type: Class
Name: BrunoRequest
Location: packages/bruno-js/src/bruno-request.js
Description: Modified to expose headerList (mutable HeaderList) and headers (raw headers object) properties.
Signature:
  headerList -> HeaderList  (mutable; tracks deletions in rawReq.__headersToDelete; includes disabled headers from rawReq.disabledHeaders)
  headers -> object  (raw headers object)

Type: Class
Name: BrunoResponse
Location: packages/bruno-js/src/bruno-response.js
Description: Modified to expose headerList (read-only HeaderList) and headers (raw headers object) properties.
Signature:
  headerList -> HeaderList  (read-only; write methods throw Error with message containing 'read-only')
  headers -> object  (raw headers object)

Type: Function
Name: translateBruToPostman
Location: packages/bruno-converters/src/utils/bruno-to-postman-translator.js
Description: Must translate req.headerList method calls to pm.request.headers equivalents and res.headerList calls to pm.response.headers. Method name mappings for request headerList: append→add, delete→remove, set→upsert; all others keep the same name. Standalone req.headerList and res.headerList (without a method call) translate to pm.request.headers and pm.response.headers respectively.

Type: Function
Name: translateCode
Location: packages/bruno-converters/src/utils/postman-to-bruno-translator.js
Description: Must translate pm.request.headers method calls to req.headerList equivalents and pm.response.headers calls to res.headerList. Method name mapping: each→forEach; all others keep the same name.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.