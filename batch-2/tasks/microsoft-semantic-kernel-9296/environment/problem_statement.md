## Description

The Semantic Kernel vector store framework needs a connector for Azure Cosmos DB NoSQL, which is currently missing. Developers who want to use Azure Cosmos DB NoSQL as a vector storage backend have no supported integration available. This prevents teams already using Azure Cosmos DB NoSQL from adopting the vector store abstraction in Semantic Kernel without writing their own low-level integration.

## Expected Behavior

- A vector store implementation that connects to an Azure Cosmos DB NoSQL database and supports creating, listing, and deleting vector collections (containers).
- A collection implementation that supports upserting, retrieving, and deleting records with vector embeddings.
- Retrieval should support optionally including or excluding the vector field in returned records.
- The connector should support custom partition keys, so that a collection can be configured with a partition path other than the default key field.
- When a model's key field has a name other than the standard property name used internally by Cosmos DB, the connector should transparently map between them during upsert and retrieval.
- When a collection does not yet exist, attempting to perform read, write, or delete operations should raise a clear, descriptive error rather than silently failing.
- The connector should be configurable via explicit parameters or through environment variables.
- The connector's underlying client should be properly closed when the connector is used as a context manager.

## Why This Matters

Teams building AI-powered applications on Azure who use Azure Cosmos DB NoSQL as their primary data store should be able to plug it directly into the Semantic Kernel vector store abstraction. Without this connector, they are forced to manually manage the low-level Cosmos DB API for vector operations, which duplicates effort and bypasses the framework's abstractions.

Additionally, the vector index configuration for another supported vector store was silently ignoring unsupported distance function values instead of raising an error. This silent failure masked misconfigurations; the behavior should be to raise an explicit error so developers catch these issues early.
