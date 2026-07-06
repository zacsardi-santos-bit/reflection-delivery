## Description

The SQLite vector store connector currently requires consumers to create, open, and pass a database connection object themselves when constructing the vector store or registering it in the dependency injection container. This means callers must manage the full connection lifecycle — opening the connection, loading any required extensions, and ensuring proper disposal — before handing it off to the library. This creates unnecessary complexity and coupling in consumer code.

## Expected Behavior

- The vector store and record collection types should accept a connection string directly, so the library can manage connection creation and disposal internally.
- The dependency injection registration helpers should accept a connection string parameter, removing the need for consumers to register a pre-opened connection object in the container.
- The internal SQL command builder should expose its operations as static utility methods that accept a connection as a parameter, rather than requiring the builder to be instantiated and hold a connection as state.

## Why This Matters

Requiring callers to manage connection state before passing it to the library is error-prone and adds boilerplate to every integration. Accepting a connection string instead makes it straightforward to configure the SQLite vector store from standard application settings (e.g., a configuration file) without writing custom connection setup code. It also allows the library to open short-lived connections per operation and clean them up properly.
