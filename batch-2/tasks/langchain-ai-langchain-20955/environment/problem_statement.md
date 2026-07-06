## Description

LangChain does not currently have built-in support for SambaNova's AI inference platforms. Developers who use SambaNova's infrastructure — either their cloud-hosted service for accessing a range of open-source models or their enterprise on-premises deployment platform — cannot take advantage of LangChain's orchestration ecosystem without writing their own custom wrappers.

## Expected Behavior

- A LangChain LLM class for the Sambaverse platform should be available, accepting a model name and optional generation parameters at construction, and reading the API key from a standard environment variable.
- A LangChain LLM class for the SambaStudio platform should be available, automatically reading all required connection details (base URL, project ID, endpoint ID, and API key) from environment variables when no explicit arguments are provided.
- Both classes should be callable through LangChain's standard LLM interface, accepting a text prompt and returning a string response from the model.

## Why This Matters

Teams building LangChain-based applications on SambaNova's infrastructure currently have to maintain custom API client code outside of LangChain's standard abstractions. Native LLM classes for both of SambaNova's platforms would let these teams use LangChain chains, agents, and pipelines seamlessly, without any bespoke integration layer.
