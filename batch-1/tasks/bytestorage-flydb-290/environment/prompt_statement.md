I'm working on FlyDB and need to implement several foundational storage components that are currently missing or incomplete. The existing write-ahead log depended on an external library that we're removing, so we need a custom replacement built on top of our own file I/O layer. We also have no support yet for column families or bloom filters.

Here's what I need:

A custom write-ahead log that takes configuration options for the storage directory, file size, log count, and save interval, and supports writing key-value records and delete records to a file-backed store. It should be able to handle hundreds of thousands of writes without errors and support periodic flushing to disk and directory cleanup.

An in-memory table that maps string keys to byte-slice values, with put, get, and delete operations. When a key doesn't exist, get should return a descriptive key-not-found error.

A memory-backed database layer that combines the write-ahead log and in-memory table. It should support large volumes of put and get operations and also expose a way to retrieve all keys currently stored.

A probabilistic data structure (bloom filter) that can be initialized with an expected item count and desired false-positive rate, supports adding byte-slice items, and provides a membership check that definitively returns false for items never added and true for items that have been added.

A column family abstraction that organizes data into named logical groups. Each column family should support creating, dropping, listing, putting, getting, deleting, and enumerating keys. Creating a column family that already exists should return an error. All of these operations should work correctly, including storing and retrieving arbitrary byte-slice values such as structured query strings.
