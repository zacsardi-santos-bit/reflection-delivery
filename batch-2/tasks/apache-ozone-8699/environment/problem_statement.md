## Description

The Recon key listing endpoint currently returns key metadata using an internal wrapper object that holds the full protobuf representation of each key. This full representation includes significantly more data than is actually needed to support key browsing, pagination, and replication-size calculations. Deserializing and holding all of this extra data per key is wasteful, especially when listing large numbers of keys.

We should replace the heavyweight wrapper with a new, lightweight data-holder class that captures only the essential fields needed for the listing API: key name, volume, bucket, path, data size, replication configuration, timestamps, parent identifier, and whether the entry is a file. The new class should be used consistently wherever the old wrapper was used — in the response type, the table accessor interface and its implementation, and throughout the endpoint logic.

## Expected Behavior

- A new lightweight key-info class is introduced in the Recon API types package and replaces the old heavy wrapper everywhere.
- The key listing response's key collection is typed to use the new lightweight class.
- The metadata table accessor method is renamed and typed to return the new class.
- The endpoint iterates, filters, and computes totals using the new lightweight class.
- Browsing and paginating through keys (both file-system-optimized and legacy bucket layouts) continues to return correct paths, keys, and replication information.

## Why This Matters

When Recon lists thousands of keys for the UI, it should not deserialize or hold in memory the full set of key metadata for each one. A focused, lightweight representation reduces memory pressure and makes the listing path more efficient without changing the observable behavior of the API.
