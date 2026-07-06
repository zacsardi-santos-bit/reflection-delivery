I'm building out the AWS HealthOmics MCP server and it's missing anything for run caches, which is a real gap since run caches let genomics workflow runs stash and reuse expensive computation results so repeated runs cost less and finish faster. I want four tools added: create, get, list, and update.

For create, it takes a caching strategy plus a cloud storage location (both required), and optional name, description, tags, and a cross-account owner hint. Before it does anything remote it should validate the caching strategy against the list of accepted values and reject invalid ones up front, and also check that the storage location's bucket actually exists and is reachable so users get a meaningful error before any cache creation is attempted. If the bucket doesn't exist the error should say the bucket was not found, if access is denied it should say access was denied, and any other storage access error should describe the trouble accessing that specific bucket. It should generate a unique request identifier, only pass along the optional params that were actually provided, and return the new cache's id, ARN, and status.

For get, look up a cache by id and return all the fields the service gives back, with any timestamp fields converted to ISO 8601 strings.

For list, support optional filtering by name, status, and caching strategy plus pagination via a token, put the caches under a designated key in the response, and only include a pagination token in the output if the service actually returned one.

For update, only send the fields explicitly provided (caching strategy, name, or description) and return the cache id plus a status showing the update succeeded.

Oh and all four should catch exceptions from the underlying service and return a dict with an error key holding the exception message text instead of letting it propagate.
