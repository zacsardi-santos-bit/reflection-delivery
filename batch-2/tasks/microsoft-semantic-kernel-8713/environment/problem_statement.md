## Description

We want to add support for a generic, dictionary-based data model to the Qdrant vector store connector, similar to what already exists for other connectors. Additionally, there are two bugs in the existing Azure AI Search generic data model mapper that need to be fixed.

## Issues to Address

### 1. Missing generic data model support in Qdrant connector

The Qdrant connector currently only supports strongly-typed record classes. Developers who want to use a flexible, schema-driven approach — passing in a record definition and working with key/value dictionaries instead of concrete model classes — have no way to do this with Qdrant. Other connectors already support this pattern, and Qdrant should too.

### 2. Azure AI Search generic mapper crashes on partial records

When mapping from a data model to storage using the Azure AI Search generic mapper, if the data model does not contain values for all properties declared in the record definition, the mapper crashes instead of gracefully skipping the missing fields. The same issue occurs in the reverse direction: when mapping from storage back to the data model, fields absent from the storage document cause an error rather than being skipped.

### 3. Azure AI Search generic mapper throws wrong exception type for missing key

When retrieving a record from Azure AI Search and the key field is absent from the stored document, the mapper throws a low-level system exception rather than a domain-specific mapping exception. This makes it harder to catch and handle predictably in application code.

## Expected Behavior

- The Qdrant connector should support using a generic, dictionary-based data model (with either numeric or UUID keys), allowing upsert and retrieval without requiring a strongly-typed record class.
- Both the Qdrant and Azure AI Search generic mappers should silently skip properties that are not present in the source data during mapping, in both directions.
- When a required key field is missing from a retrieved record, the mapper should raise a domain-specific mapping exception with a clear message identifying which key property was missing.

## Why This Matters

This gives developers the flexibility to work with vector store records in a schema-driven but loosely-structured way, which is especially useful for dynamic or multi-tenant scenarios where the schema may vary or where a dedicated strongly-typed class is not practical.
