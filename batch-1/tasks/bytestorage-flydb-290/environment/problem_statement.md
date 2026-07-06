## Description

FlyDB needs several new foundational storage components: a custom write-ahead log, an in-memory table layer, a bloom filter, and a column-family abstraction. The existing WAL relied on a third-party library that is being removed, and there was no built-in support for column families or bloom filters at all.

## Expected Behavior

- A bloom filter component that supports adding items and checking whether an item might be present. Items that were never added must be definitively rejected; items that were added must be recognized as potentially present.
- A custom write-ahead log that accepts configuration options (storage path, log count, file size, save interval) and exposes operations to store key-value records durably, flush data to disk, and clean up storage. It must be able to handle high write volumes without errors.
- An in-memory table that stores string keys mapped to byte-slice values. Looking up a key that does not exist must return a clear key-not-found error.
- A memory-backed database layer that wraps the WAL and in-memory table, supporting put, get, and key listing operations for large numbers of entries.
- A column family abstraction that groups separate logical data namespaces. It must support creating, listing, and dropping column families, as well as putting, getting, deleting, and listing keys within a named family. Creating a column family that already exists must return an error. Arbitrary values including structured strings must round-trip correctly through put and get.

## Why This Matters

These components are the foundation of FlyDB's tiered storage architecture. Without them, the database cannot durably log mutations, organize data into namespaces, or use probabilistic data structures to speed up lookups.
