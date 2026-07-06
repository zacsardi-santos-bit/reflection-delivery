Implement support for the Elasticsearch Search Shards API in the Go client library. Create a method to query the cluster for shard information for specified indices, returning structured data about shard distribution and state.

*   Update the `Client` type:
    *   Implement the `SearchShards` method in `client.go` with the signature:
        ```go
        func (c *Client) SearchShards(indices ...string) *SearchShardsService
        ```
    *   Ensure it returns a new `SearchShardsService` for the given index names.

*   Create the `SearchShardsService` struct in `search_shards.go`:
    *   Implement the constructor:
        ```go
        func NewSearchShardsService(client *Client) *SearchShardsService
        ```
    *   Implement the `Index` method to set target indices:
        ```go
        func (s *SearchShardsService) Index(index ...string) *SearchShardsService
        ```
    *   Implement the `Do` method to execute the API request:
        ```go
        func (s *SearchShardsService) Do(ctx context.Context) (*SearchShardsResponse, error)
        ```

*   Define the `SearchShardsResponse` struct in `search_shards.go`:
    *   Include the following fields:
        *   `Nodes` of type `map[string]interface{}` (JSON: "nodes")
        *   `Indices` of type `map[string]interface{}` (JSON: "indices")
        *   `Shards` as a 2D slice of `ShardsInfo` (i.e., `[][]ShardsInfo`), decoded from JSON field "shards".

*   Define the `ShardsInfo` struct in `search_shards.go`:
    *   Include at minimum the following fields:
        *   `Index` (string, JSON: "index") — the index name
        *   `State` (string, JSON: "state") — the shard's operational state
        *   `Node` (string, JSON: "node")
        *   `Primary` (bool, JSON: "primary")
        *   `Shard` (uint, JSON: "shard")

*   Ensure the `SearchShardsService`:
    *   Returns a non-nil response when executed against a live cluster.
    *   Populates `Shards` with at least one entry, where `Shards[0][0].Index` matches the requested index name.

*   Place the implementation in `search_shards.go` within the `elastic` package.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.