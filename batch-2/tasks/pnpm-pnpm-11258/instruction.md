I'd like to add a native ping command to pnpm so users can test connectivity to their configured registry without needing external tools.

*   The ping module must export a commandNames array equal to ['ping'].

*   The ping module's help() function must return a string that contains 'Test connectivity' and 'pnpm ping'.

*   The ping module's cliOptionsTypes() function must return an object that has a 'registry' property.

*   The ping module's rcOptionsTypes() function must return an object.

*   The handler function must make a GET request to {registryUrl}/-/ping?write=true, where registryUrl is taken from the registry option if provided, or from registries.default if registry is not specified.

*   When the registry responds with HTTP 200 and an empty or minimal JSON body (e.g. {}), handler must return a string that exactly matches the pattern: PING {registryUrl}\nPONG {N}ms — where {N} is the round-trip time in milliseconds as a plain integer.

*   When the registry responds with HTTP 200 and a non-empty JSON object body (containing at least one key), handler must return a string that contains the PING line, the PONG timing line, and the body's fields pretty-printed in JSON style (e.g. '"host": "npm"').

*   When the handler is called without a registry option but with a registries.default value, it must use the default registry URL to construct the ping request.

*   When the registry returns a non-2xx HTTP status code (such as 401, 403, 404, or 500), handler must throw an error whose message contains the substring 'Failed to reach registry'.

*   When a network-level error occurs (e.g. connection refused), handler must throw an error whose message contains the substring 'Failed to reach registry'.

*   When the registry URL contains a path prefix and does not end with a trailing slash (e.g. https://host/custom-prefix), the ping request must be sent to {prefix}/-/ping?write=true and the PING output line must display the original registry URL without modification.


*   Interface details: Type: Module
Name: ping
Location: registry-access/commands/src/ping.ts
Description: A command module that tests connectivity to an npm registry. Must be exported as a namespace from registry-access/commands/src/index.ts. The module exposes the following members:

  commandNames: string[]
    Value must equal ['ping'].

  help(): string
    Returns a help string for the command. The string must contain "Test connectivity" and "pnpm ping".

  cliOptionsTypes(): Record<string, unknown>
    Returns an object describing CLI option types. The returned object must have a 'registry' property.

  rcOptionsTypes(): Record<string, unknown>
    Returns an object describing RC configuration option types.

  handler(opts: PingOptions): Promise<string>
    Signature: handler(opts: { registry?: string, registries?: { default: string } }) => Promise<string>
    Async function that pings the registry and returns a formatted result string.
    - If opts.registry is provided, uses it as the registry URL; otherwise uses opts.registries.default.
    - Makes a GET request to {registryUrl}/-/ping?write=true (preserving any path prefix in the URL).
    - On success (HTTP 2xx) with an empty or minimal JSON body (e.g. {}), returns a string matching the pattern: "PING {registryUrl}\nPONG {N}ms"
    - On success with a non-empty JSON object body, returns a string that includes "PING {registryUrl}", "PONG {N}ms", and the body pretty-printed in JSON format (e.g. with "key": "value" pairs).
    - On non-2xx HTTP responses: throws an error whose message contains "Failed to reach registry".
    - On network failure: throws an error whose message contains "Failed to reach registry".
    - When the registry URL has a path prefix without a trailing slash (e.g. https://host/prefix), the ping URL must append /-/ping to that prefix path (e.g. /prefix/-/ping?write=true).


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.