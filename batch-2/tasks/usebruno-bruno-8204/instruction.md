I'm working on the OpenAPI sync feature in Bruno.

*   The openapi-sync module must export a _test object containing the following functions as properties: maskJsonInterpolations, unmaskJsonInterpolations, mergeJsonValues, mergeJsonBody, mergeSpecIntoRequest, compareRequestFields, mergeFieldListPreserving, mergeAuth, and mergeBody.

*   maskJsonInterpolations(jsonString) must return { masked, vars } where masked is valid JSON (bare {{var}} references are quoted), vars is an array of the original variable tokens found as bare values. Variables already inside string values must be left untouched inside their surrounding string. The function must handle backslash-escaped characters (e.g. Windows paths) without corrupting the output. It must not corrupt literal string content that resembles internal sentinel tokens.

*   unmaskJsonInterpolations(maskedString, vars) must restore the original variable references from the array returned by maskJsonInterpolations. After restoration, in-string variables must remain wrapped in their surrounding quotes, bare-value variables must remain unquoted, and variables used as object keys must be preserved correctly.

*   mergeJsonValues(userObj, specObj, preserveValues) must merge two plain objects: when preserveValues=true, user values win for shared keys, keys absent from spec are dropped, and keys spec introduces are added with spec's value. Nested objects are recursively merged by the same rules. For array fields, each user element is merged against the first spec element as a shape template (adding new fields, removing dropped ones); an empty user array uses the spec's first element as the result; an array of primitives is kept as-is. When preserveValues=false, the function returns the spec object's values.

*   mergeJsonBody(userBody, specBody, preserveValues) must accept body objects with a mode and json property. When preserveValues=true it merges the parsed JSON objects using mergeJsonValues, preserves {{var}} references, and returns a new body object whose json string is structurally valid once variables are replaced. If the user json string cannot be parsed, it must be returned verbatim. When preserveValues=false it must return specBody directly (same reference).

*   mergeFieldListPreserving(spec, user, preserveValues=true) must merge two arrays of field objects (each with at least a name property). When preserveValues=true: user value and enabled flag are kept for entries whose name matches a spec entry; spec entries missing from user are added with spec values; user entries not present in spec are dropped; duplicate names are paired positionally. File-type entries (value is an array) must have their value array preserved. When a user entry lacks an enabled property, the spec entry's enabled value is used. When preserveValues=false, the spec array is returned unchanged.

*   mergeAuth(user, spec, preserveValues=true) must merge auth configuration objects. When the mode matches and preserveValues=true: user values win on shared sub-object fields; spec-introduced sub-object fields are added; user-only sub-object fields are NOT deleted; the return value must be a defensive clone (not aliasing the user auth sub-object). When the mode differs, the spec auth object is returned. When the user's auth sub-object is null, the spec auth is returned. When preserveValues=false, the spec auth is returned (full overwrite, dropping user-only fields).

*   mergeBody(user, spec, preserveValues=true) must dispatch on the body mode: json bodies are merged at the field level using mergeJsonBody; formUrlEncoded bodies are merged by name using mergeFieldListPreserving; xml, text, and other raw-body modes return the user body verbatim when modes match; when body modes differ the spec body is returned. For graphql mode: when the user has a graphql sub-object, return a defensive clone of user.graphql; when the user has no graphql sub-object, fall back to spec.graphql.

*   mergeSpecIntoRequest(existing, specItem, options={}) must merge a spec request item into an existing request object. By default (sync mode): the URL is always taken from the spec; body, params, headers, and auth are merged preserving user values via their respective merge helpers; the script, tests, and assertions fields of the existing request are always preserved unchanged regardless of spec. When body mode changes the spec body wins outright. When options.preserveValues=false, spec values overwrite user values in body and params. When options.fullReset=true, body, auth, and method are taken directly from the spec, but script, tests, and assertions are still preserved.

*   compareRequestFields(spec, actual) must return { hasDiff, changes } where hasDiff is a boolean and changes is an array of strings. When comparing auth: if both spec and actual have the same auth mode, hasDiff must be false even if the config values differ. If the auth modes differ, hasDiff must be true and the changes array must contain the string 'auth'.


*   Interface details: Type: Module Export
Name: _test
Location: packages/bruno-electron/src/ipc/openapi-sync.js
Description: The openapi-sync module must export a _test property containing all the helper functions below so they can be accessed as syncModule._test.<functionName>.

---

Type: Function
Name: maskJsonInterpolations
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: maskJsonInterpolations(jsonString) -> { masked: string, vars: string[] }
Description: Accepts a JSON-like string that may contain bare {{var}} interpolations outside of string values. Returns an object where masked is a valid JSON string (bare variables have been temporarily quoted or replaced with a sentinel) and vars is an array of the original {{var}} tokens that were found as bare values. Variables that appear inside existing string values are left in place and do not appear in vars.

---

Type: Function
Name: unmaskJsonInterpolations
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: unmaskJsonInterpolations(maskedString, vars) -> string
Description: Reverses the masking performed by maskJsonInterpolations. Accepts the masked JSON string and the vars array. Returns a string where bare-value sentinels are replaced with the original {{var}} tokens (unquoted), and in-string variables retain their surrounding quotes.

---

Type: Function
Name: mergeJsonValues
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: mergeJsonValues(userObj, specObj, preserveValues) -> object
Description: Merges two plain JS objects. When preserveValues=true: user values win for shared keys, spec-only keys are added, user-only keys are dropped, nested objects are recursively merged. Arrays are merged by mapping each user element against the first spec element as a shape template; an empty user array uses the spec's first element. Arrays of primitives are kept as-is. When preserveValues=false: returns the spec object.

---

Type: Function
Name: mergeJsonBody
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: mergeJsonBody(userBody, specBody, preserveValues) -> object
Description: Merges two body objects that have a mode and json property. When preserveValues=true: parses both json strings, calls mergeJsonValues, re-serializes, and preserves {{var}} references. If the user json is unparseable, returns userBody verbatim (same json string). When preserveValues=false: returns specBody directly (same reference).

---

Type: Function
Name: mergeFieldListPreserving
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: mergeFieldListPreserving(spec, user, preserveValues = true) -> array
Description: Merges two arrays of field objects (each with a name property and optionally value, enabled, type). When preserveValues=true: match by name (positionally for duplicates), keep user value and enabled, add spec-only entries, drop user-only entries, preserve file value arrays, fall back to spec enabled when user has none. When preserveValues=false: returns the spec array unchanged.

---

Type: Function
Name: mergeAuth
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: mergeAuth(user, spec, preserveValues = true) -> object
Description: Merges two auth configuration objects (each with a mode and a mode-named sub-object like oauth2). When preserveValues=true and modes match: user values win on shared sub-object fields, spec-introduced fields are added, user-only fields are NOT deleted, result is a defensive clone. When modes differ: returns spec. When user auth sub-object is null: returns spec. When preserveValues=false: returns spec (full overwrite, dropping user-only fields).

---

Type: Function
Name: mergeBody
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: mergeBody(user, spec, preserveValues = true) -> object
Description: Dispatches body merging by mode. json -> calls mergeJsonBody; formUrlEncoded -> calls mergeFieldListPreserving on the formUrlEncoded arrays; xml/text and other raw modes -> returns user body verbatim when modes match; mode mismatch -> returns spec. For graphql mode: returns a defensive clone of user.graphql when present; falls back to spec.graphql when user has none.

---

Type: Function
Name: mergeSpecIntoRequest
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: mergeSpecIntoRequest(existing, specItem, options = {}) -> object
Description: Merges a spec request item into an existing request object. options may contain preserveValues (boolean, default true) and fullReset (boolean, default false). In sync mode (fullReset=false): URL always from spec; body/params/headers/auth merged preserving user values; script/tests/assertions always preserved. Mode change in body -> spec body wins. When preserveValues=false: spec values used for body and params. When fullReset=true: body/auth/method taken from spec; script/tests/assertions still preserved.

---

Type: Function
Name: compareRequestFields
Location: packages/bruno-electron/src/ipc/openapi-sync.js (exported via _test)
Signature: compareRequestFields(spec, actual) -> { hasDiff: boolean, changes: string[] }
Description: Compares two request field objects and returns { hasDiff, changes }. For auth comparison: same auth mode with different config values -> hasDiff=false. Different auth modes -> hasDiff=true and changes array contains the string 'auth'.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.