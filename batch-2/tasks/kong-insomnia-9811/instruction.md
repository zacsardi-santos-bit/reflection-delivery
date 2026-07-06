I'm working on the Konnect sync feature in Insomnia, and there are two improvements I need to make.

*   A new module at packages/insomnia/src/konnect/transform.ts must be created and must export extractRegionFromEndpoint, deriveProxyVarDefaults, generatePathPlaceholder, mergeHeaders, mergePathParameters, konnectHeadersChanged, pathParametersChanged, and KONNECT_PROXY_VAR_NAMES.

*   extractRegionFromEndpoint must be moved from api.ts to transform.ts and removed from api.ts exports. It must parse URLs of the form https://<id>.<region>.cp0.konghq.com to extract the region segment, and fall back to 'us' for malformed URLs, empty strings, or hostnames that don't match that pattern.

*   KONNECT_PROXY_VAR_NAMES must be moved from api.ts to transform.ts as a readonly tuple: ['proxy_host', 'grpc_proxy_host', 'grpcs_proxy_host'].

*   deriveProxyVarDefaults must return an empty object when its input is null or an empty array. For http and ws protocol entries it sets proxy_host to host (omitting port 80) or host:port for non-standard ports. For https and wss entries it sets proxy_host omitting port 443. For grpc it always sets grpc_proxy_host as host:port. For grpcs it always sets grpcs_proxy_host as host:port. Protocol matching is case-insensitive. The first matching entry per protocol family is used; entries with empty host are skipped.

*   generatePathPlaceholder must strip leading ^ and trailing $ anchors, unescape \/ to / and \. to ., and normalise an optional trailing /? to /. Named capture groups (?<Name>...) become :name (lowercased) and are added to pathParameters with an empty value. Unnamed capture groups ([...]) and stray character classes like [a-z]+ share a single incrementing counter and become :param_1, :param_2, etc. If leftover regex characters remain after substitution (including backslash shorthands like \d+), the function falls back: with default mode to { path: '/:path', pathParameters: [{ name: 'path', value: '' }] }, and with fallbackMode='keep' to { path: regexString, pathParameters: [] }. Plain paths with no regex characters are returned unchanged with an empty pathParameters array.

*   mergeHeaders must return incoming headers when existing is empty. It must preserve user-added headers that are not in the prevManagedNames list and not in the incoming set. Previously managed headers (in prevManagedNames) that are no longer in the incoming set must be removed.

*   mergePathParameters must map over the incoming parameter list and preserve any user-filled value from the existing list for each parameter name that still exists; new parameters receive an empty value; parameters no longer in the incoming list are dropped.

*   konnectHeadersChanged must return false when the managed portion of the existing headers is identical to the incoming list. It must return true when a managed header value changes, when a managed header is removed (incoming is empty but prevManagedNames is non-empty), or when a new managed header is added. It must return false when incoming is empty and prevManagedNames is also empty.

*   pathParametersChanged must return false when both lists are empty or when the names match exactly (ignoring values). It must return true when a parameter is added, removed, or renamed.

*   When syncing an HTTP route whose path is a regex with backslash shorthands (e.g. ~/regex/\d+), the resulting request URL must be http://{{ _.proxy_host }}/:path and pathParameters must be [{ name: 'path', value: '' }]. The request name must be the original tilde-prefixed path string (e.g. ~/regex/\d+).

*   When syncing an HTTP route whose path is a regex with a named capture group (e.g. ~/api/users/(?<userId>[0-9]+)), the resulting request URL must replace the capture group with a lowercased colon parameter (e.g. http://{{ _.proxy_host }}/api/users/:userid) and pathParameters must be [{ name: 'userid', value: '' }]. The request name must be the derived path (e.g. /api/users/:userid).

*   On re-sync, if the regex path is unchanged the existing user-filled pathParameters values must be preserved. If the regex changes such that it produces a different route key (e.g. a renamed capture group), the old request must be deleted and a new one created with empty path parameter values.

*   When the control plane has a proxy_urls array, sync must auto-fill any empty proxy environment variables (proxy_host, grpc_proxy_host, grpcs_proxy_host) using the values derived from that array. Proxy variable values that the user has already filled in must never be overwritten. On re-sync, previously empty vars must be filled when proxy_urls becomes available.


*   Interface details: Type: Function
Name: extractRegionFromEndpoint
Location: packages/insomnia/src/konnect/transform.ts
Signature: extractRegionFromEndpoint(endpoint: string): string
Description: Extracts the Konnect region string from a control plane endpoint URL (e.g. "https://abc123.us.cp0.konghq.com" → "us"). Falls back to "us" for malformed, empty, or unrecognised URLs. Previously exported from api.ts; must now be exported from transform.ts.

Type: Function
Name: deriveProxyVarDefaults
Location: packages/insomnia/src/konnect/transform.ts
Signature: deriveProxyVarDefaults(proxyUrls: { host: string; port: number; protocol: string }[] | null | undefined): Partial<Record<'proxy_host' | 'grpc_proxy_host' | 'grpcs_proxy_host', string>>
Description: Derives default values for the three proxy environment variables from a control plane's proxy_urls array. Returns an empty object when the input is null or an empty array. Protocol matching is case-insensitive.

Type: Function
Name: generatePathPlaceholder
Location: packages/insomnia/src/konnect/transform.ts
Signature: generatePathPlaceholder(regexString: string, fallbackMode?: 'keep' | 'replace'): { path: string; pathParameters: { name: string; value: string }[] }
Description: Converts a Kong regex path string into a URL path using colon-style parameters and a corresponding pathParameters array. Default fallbackMode is 'replace'. With 'replace': complex-to-parse patterns fall back to { path: '/:path', pathParameters: [{ name: 'path', value: '' }] }. With 'keep': falls back to { path: regexString, pathParameters: [] }.

Type: Function
Name: mergeHeaders
Location: packages/insomnia/src/konnect/transform.ts
Signature: mergeHeaders(existing: { name: string; value: string }[], incoming: { name: string; value: string }[], prevManagedNames: string[]): { name: string; value: string }[]
Description: Merges Konnect-managed headers into the existing header array. Previously managed headers (listed in prevManagedNames) that are no longer incoming are removed. User-added headers outside that set are always preserved.

Type: Function
Name: mergePathParameters
Location: packages/insomnia/src/konnect/transform.ts
Signature: mergePathParameters(existing: { name: string; value: string }[], incoming: { name: string; value: string }[]): { name: string; value: string }[]
Description: Merges Konnect-derived path parameters into the existing set. Preserves user-filled values for params that still appear in the incoming list; drops removed params; adds new params with an empty value.

Type: Function
Name: konnectHeadersChanged
Location: packages/insomnia/src/konnect/transform.ts
Signature: konnectHeadersChanged(existing: { name: string; value: string }[], incoming: { name: string; value: string }[], prevManagedNames: string[]): boolean
Description: Returns true if the Konnect-managed portion of the header set has changed compared to the incoming headers. Uses prevManagedNames to detect removal of all managed headers when incoming is empty.

Type: Function
Name: pathParametersChanged
Location: packages/insomnia/src/konnect/transform.ts
Signature: pathParametersChanged(existing: { name: string; value: string }[], incoming: { name: string; value: string }[]): boolean
Description: Returns true if the path parameter structure (names and count) has changed between the existing and incoming sets. User-filled values are not considered — only param names and count.

Type: Constant
Name: KONNECT_PROXY_VAR_NAMES
Location: packages/insomnia/src/konnect/transform.ts
Signature: KONNECT_PROXY_VAR_NAMES: readonly ['proxy_host', 'grpc_proxy_host', 'grpcs_proxy_host']
Description: The three proxy environment variable names managed by Konnect sync. Previously exported from api.ts; must now be exported from transform.ts.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.