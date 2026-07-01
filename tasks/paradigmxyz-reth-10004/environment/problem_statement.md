## Description

The trie library currently contains database-specific cursor implementations alongside its core, database-agnostic logic. This creates an inappropriate coupling: types that require direct database access are defined and exported from a package that is meant to be a general-purpose trie abstraction. Any consumer of the trie library therefore gets database-specific types mixed in with the core trie API, even if they don't need them.

There is a dedicated package for integrating the trie layer with the database, but the database-specific cursor and factory implementations are not housed there. Instead they live inside the core trie library, which makes the boundary between the two layers unclear.

## Expected Behavior

- The database-specific hashed cursor factory and its account/storage cursor implementations should live in the database integration package, not the core trie library.
- The database-specific trie cursor factory and its account/storage trie cursor implementations should also live in the database integration package.
- All of these types should be publicly accessible from the database integration package.
- After the move, the core trie library should no longer export these database-specific types.
- Any existing code that imports these types from the core trie library must instead import them from the database integration package.

## Why This Matters

Keeping the trie abstraction layer free of direct database dependencies makes the codebase easier to understand, test, and maintain. The database integration package is the correct home for implementations that require direct database access, and consolidating them there makes the separation of concerns explicit and enforced.
