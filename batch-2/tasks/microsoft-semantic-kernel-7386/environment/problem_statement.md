## Description

The vector store connectors (Azure AI Search, Qdrant, Redis) and the built-in in-memory vector store currently lack dependency injection registration helpers. Developers have to manually instantiate and register these stores in the service container, which introduces boilerplate and is inconsistent with how other services are registered in the framework.

We should add extension methods for both the standard service collection and the AI kernel builder that let developers register any of the supported vector store backends with a single method call. The extension methods should support multiple connection styles — for example, using an existing client already in the container or providing connection details directly.

## Expected Behavior

- Calling the registration method on either the service collection or the kernel builder should make the vector store resolvable as the vector store interface.
- The resolved instance must be the correct concrete implementation for the chosen backend.
- Registration variants that accept connection details (endpoint, credentials, host/port, etc.) should create the underlying client internally.
- Registration variants that take no connection arguments should resolve the required client from the existing DI container.

## Why This Matters

Without these helpers, every application that wants to use a vector store must duplicate the wiring logic, increasing the risk of misconfiguration and making it harder to switch backends. Providing first-class DI registration methods brings vector stores in line with the rest of the framework's service registration patterns.
