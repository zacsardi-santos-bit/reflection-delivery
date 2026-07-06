Remove the token-counting method from the core language model interface in the Go LLM library. Ensure that all chain operations continue to function correctly without requiring each model implementation to provide token counting.

*   Update the LLM interface in the `llms/llms.go` file:
    *   Remove the `GetNumTokens(text string) int` method from the LLM interface definition.
    *   Ensure that types implementing the LLM interface are not required to implement the `GetNumTokens` method.

*   Verify the functionality of all chain operations:
    *   Ensure that LLM chains, sequential chains, simple sequential chains, map-reduce chains, map-rerank chains, and document chains continue to operate correctly without the `GetNumTokens` method in the LLM interface.

*   Implement token counting as a standalone utility function if necessary, ensuring it is decoupled from the LLM interface.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.