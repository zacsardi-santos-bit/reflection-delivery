Refactor the certificate management and proxy bypass logic in the networking library to improve efficiency and flexibility. Implement a caching mechanism for SSL certificates and enhance the proxy bypass logic to support port-specific matching and programmatic pattern specification.

*   Implement `getCertificateContent` in `packages/fetch/src/certs.ts`:
    *   Parse data URIs by splitting on the first comma into a header and data portion.
    *   Decode data from base64 if the header contains 'base64', otherwise URL-decode it.
    *   Treat inputs not starting with 'data:' as file paths and read them synchronously using `fs.readFileSync(input, 'utf8')`.

*   Create a singleton class `CertsCache` in `packages/fetch/src/certs.ts`:
    *   Include a static method `getInstance()` to return the singleton instance.
    *   Declare private fields `_fixedCa` (string[]), `_initialized` (boolean), and `_customCerts` (Map<string, string>).
    *   Implement a public getter `fixedCa` that lazily initializes on first access:
        *   Read and include the content of the file path specified in the `NODE_EXTRA_CA_CERTS` environment variable.
        *   Combine it with system root TLS certificates and store in `_fixedCa`.
        *   Set `_initialized` to true; return cached `_fixedCa` on subsequent accesses.
    *   Implement `getCachedCustomCert(path: string)`:
        *   Check `_customCerts` for cached entries before reading.
        *   On cache miss, call `getCertificateContent(path)`, store the result in `_customCerts`, and return it.
    *   Implement `getAllCachedCustomCerts(caBundlePath: string[] | string)`:
        *   Retrieve each cert via `getCachedCustomCert` and return an array of certificate strings.
    *   Implement `getCa(caBundlePath: undefined | string | string[])`:
        *   Return `[...fixedCa, ...customCerts]` when `caBundlePath` is provided, or just `fixedCa` when undefined or falsy.
    *   Implement `clear()`:
        *   Synchronously clear `_customCerts`, set `_initialized` to false, and set `_fixedCa` to an empty array.

*   Update `getAgentOptions` in `packages/fetch/src/getAgentOptions.ts`:
    *   Change to an async function with signature `getAgentOptions(requestOptions?: RequestOptions): Promise<{ [key: string]: any }>`
    *   Use `CertsCache.getInstance().getCa()` to retrieve the CA list.

*   Implement `patternMatchesHostname` in `packages/fetch/src/util.ts`:
    *   Normalize both arguments to lowercase and split each on ':' to separate host from port.
    *   Implement port matching: if pattern includes a port, hostname must include the same port to match.
    *   Implement hostname matching: exact equality, wildcard, and suffix matching.

*   Update `shouldBypassProxy` in `packages/fetch/src/util.ts`:
    *   Accept a second parameter `requestOptions` of type `RequestOptions | undefined`.
    *   Combine patterns from `NO_PROXY/no_proxy` environment variables with `requestOptions.noProxy` (if provided).
    *   Check the hostname against all combined patterns using `patternMatchesHostname`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.