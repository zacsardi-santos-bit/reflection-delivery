Implement security and normalization utilities for the agent utilities module to enhance network connection safety and standardize agent configuration formats.

*   Implement `getGrpcCredentials(url: string): grpc.ChannelCredentials` in `packages/core/src/agents/a2aUtils.ts`.
    *   Return secure (SSL) credentials for URLs starting with "https://".
    *   Return insecure credentials for all other URLs (e.g., "http://").
    *   Ensure the returned value is defined (non-null, non-undefined).

*   Implement `pinUrlToIp(url: string, agentName: string): Promise<{ pinnedUrl: string; hostname: string }>` in `packages/core/src/agents/a2aUtils.ts`.
    *   Resolve the hostname in the URL to its IP address via DNS lookup.
    *   Return an object with:
        *   `hostname`: the original hostname extracted from the URL.
        *   `pinnedUrl`: the URL with the hostname replaced by the resolved IP.
    *   Preserve the scheme for full URLs (e.g., "http://example.com:9000").
    *   For "host:port" strings without a scheme, return without adding a scheme.
    *   Throw an error if DNS resolution fails, including "Failed to resolve host for agent '${agentName}'" in the message.
    *   Throw an error if the resolved IP is in a private network range, except for "localhost", "127.0.0.1", and "::1".

*   Implement `normalizeAgentCard(card: unknown): AgentCard` in `packages/core/src/agents/a2aUtils.ts`.
    *   Throw an error with "Agent card is missing." if the input is null, undefined, or not a plain object.
    *   Preserve all unknown/custom fields from the input object.
    *   Provide default values for missing fields:
        *   `description`: empty string.
        *   `skills`: empty array.
        *   `defaultInputModes`: empty array.
    *   Normalize interface definitions from "additionalInterfaces" or "supportedInterfaces".
    *   Write normalized interfaces to both "additionalInterfaces" and "supportedInterfaces".
    *   Within each interface:
        *   Set `transport` to `protocolBinding` if absent, but do not override if `transport` is already set.
        *   Prepend "http://" to URLs without a scheme unless the transport is "GRPC".
    *   Set the top-level `url` to the first normalized interface's URL if unset or empty.

*   Implement `splitAgentCardUrl(url: string): { baseUrl: string; path?: string }` in `packages/core/src/agents/a2aUtils.ts`.
    *   If the URL's pathname ends with ".well-known/agent-card.json", strip it and return the base URL.
    *   Return the original URL unchanged if the pathname does not end with the standard path, the path appears earlier, or URL parsing fails.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.