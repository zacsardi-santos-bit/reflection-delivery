## Description

The Elasticsearch client library is missing support for the Search Shards API, which allows developers to discover which shards a search request would be executed against for a given set of indices.

This is listed in the README as a planned but unimplemented feature. Without it, developers who need to understand shard distribution — for routing decisions, query optimization, or verifying cluster state — have no built-in way to retrieve this information and must resort to manually crafting raw HTTP requests.

## Expected Behavior

- The client should expose a method that accepts one or more index names and returns a service object that can be executed against the cluster.
- When executed, the service should return structured information about the shards that would handle the search, including at minimum the index name each shard belongs to and its current operational state.
- The returned shard information should be organized as a nested collection, where each entry includes the relevant index and state details.
- For active, running shards on a live cluster, the state reported should reflect that the shards are started and operational.

## Why This Matters

Shard-level visibility is important for cluster management, debugging, and building advanced routing logic. Many Elasticsearch features depend on knowing where data lives, and the absence of this API wrapper forces users to bypass the library entirely.
