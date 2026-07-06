I've noticed that the Azure Assistant provider is leaking sensitive data into cache keys and debug logs.

*   When callApi is invoked on the AzureAssistantProvider, the cache key must match the format 'azure_assistant:<deploymentName>:<hash>' where <hash> is exactly 64 lowercase hexadecimal characters (the output of a SHA-256 or HMAC-SHA256 digest).

*   The cache key must not contain the prompt text, assistant instructions, Azure API keys, or Azure endpoint hostnames in any form — all sensitive inputs must be hashed into an opaque digest.

*   Different Azure API base URLs or different authentication header values must produce different cache key hashes, even when the prompt and other configuration fields are identical.

*   The same combination of prompt, authentication headers, API endpoint, and assistant configuration must always produce the same cache key, even after module reloads or process restarts — the hash must be fully deterministic and not seeded with runtime-random values.

*   When callApi finds a cache hit under the computed key, it must return an object of the form { output: <cached value>, cached: true } without invoking the internal makeRequest method.

*   All debug log messages emitted during callApi execution must not include the prompt text; log messages should use metadata such as prompt length rather than the prompt content itself.


**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.