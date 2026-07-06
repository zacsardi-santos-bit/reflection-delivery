Implement extension methods for registering vector store backends in a .NET application using dependency injection. Ensure these methods allow for easy registration of Azure AI Search, Qdrant, Redis, and in-memory vector stores through both the standard service collection and the AI kernel builder.

*   Implement `AzureAISearchKernelBuilderExtensions` class:
    *   Provide `AddAzureAISearchVectorStore` extension method on `IKernelBuilder` with no connection parameters to retrieve `SearchIndexClient` from DI and register `AzureAISearchVectorStore` as `IVectorStore`.
    *   Provide overloads accepting `(Uri endpoint, AzureKeyCredential credential)` and `(Uri endpoint, TokenCredential credential)` to register `AzureAISearchVectorStore` as `IVectorStore`.

*   Implement `AzureAISearchServiceCollectionExtensions` class:
    *   Provide `AddAzureAISearchVectorStore` overloads on `IServiceCollection` similar to `IKernelBuilder` to register `AzureAISearchVectorStore` as `IVectorStore`.

*   Implement `QdrantKernelBuilderExtensions` class:
    *   Provide `AddQdrantVectorStore` extension method on `IKernelBuilder` with optional `host` parameter. Use existing `QdrantClient` from DI if `host` is null, otherwise create a new client. Register `QdrantVectorStore` as `IVectorStore`.

*   Implement `QdrantServiceCollectionExtensions` class:
    *   Provide `AddQdrantVectorStore` method on `IServiceCollection` to register `QdrantVectorStore` as `IVectorStore`.

*   Implement `RedisKernelBuilderExtensions` class:
    *   Provide `AddRedisVectorStore` extension method on `IKernelBuilder` to retrieve `IDatabase` from DI and register `RedisVectorStore` as `IVectorStore`.

*   Implement `RedisServiceCollectionExtensions` class:
    *   Provide `AddRedisVectorStore` method on `IServiceCollection` to register `RedisVectorStore` as `IVectorStore`.

*   Implement `KernelBuilderExtensions` class in `Microsoft.SemanticKernel.Data` namespace:
    *   Provide `AddVolatileVectorStore` method on `IKernelBuilder` to register `VolatileVectorStore` as `IVectorStore`.

*   Implement `ServiceCollectionExtensions` class in `Microsoft.SemanticKernel.Data` namespace:
    *   Provide `AddVolatileVectorStore` method on `IServiceCollection` to register `VolatileVectorStore` as `IVectorStore`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.