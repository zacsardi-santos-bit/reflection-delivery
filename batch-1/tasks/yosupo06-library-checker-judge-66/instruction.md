Fix the API server issues to ensure it functions correctly in test environments and is protected against abuse. Implement the following changes to address startup crashes, language list retrieval, and submission size validation.

*   Ensure the server starts without crashing:
    *   Configure the server to use a default authentication secret if the environment variable is not set.
    *   Verify that the server binds to localhost on port 50051 using insecure transport (no TLS).

*   Correct the language list retrieval:
    *   Update the server to locate the language definitions file in the `api/` directory.
    *   Ensure the language list RPC endpoint returns a non-empty list of supported languages.

*   Implement submission size validation:
    *   Set a maximum size limit for source code submissions at less than 3,000,000 bytes.
    *   Ensure the submission RPC endpoint rejects any source code payload exceeding 1 MiB (1,048,576 bytes) with a non-nil error message.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.