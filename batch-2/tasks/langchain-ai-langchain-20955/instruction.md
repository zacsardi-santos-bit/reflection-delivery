Implement native LangChain LLM classes for SambaNova's Sambaverse and SambaStudio platforms to facilitate seamless integration into LangChain applications. Ensure these classes adhere to the standard LangChain LLM interface and handle necessary configurations via environment variables.

*   Implement the Sambaverse class:
    *   Import from `langchain_community.llms.sambanova`.
    *   Accept `sambaverse_model_name` (str) and `model_kwargs` (dict) as constructor parameters.
    *   Read the API key from the `SAMBAVERSE_API_KEY` environment variable if not explicitly provided.
    *   Implement the `invoke(prompt: str) -> str` method, ensuring it returns a non-empty string.
    *   Use `requests.Session` for HTTP communication with the Sambaverse API.
    *   Parse the API response to extract and return the completion text as a plain string.

*   Implement the SambaStudio class:
    *   Import from `langchain_community.llms.sambanova`.
    *   Allow instantiation without explicit constructor arguments.
    *   Automatically read `SAMBASTUDIO_BASE_URL`, `SAMBASTUDIO_PROJECT_ID`, `SAMBASTUDIO_ENDPOINT_ID`, and `SAMBASTUDIO_API_KEY` from the environment.
    *   Implement the `invoke(prompt: str) -> str` method, ensuring it returns a non-empty string.
    *   Use `requests.Session` for HTTP communication with the SambaStudio API.
    *   Parse the API response to extract and return the completion text as a plain string.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.