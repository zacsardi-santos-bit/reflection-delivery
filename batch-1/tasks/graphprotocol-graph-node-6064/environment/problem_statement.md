## Description

When looking up a block by its hash to retrieve its number and timestamp, the current API returns the timestamp wrapped in an internal time type. This requires every caller to perform an additional conversion step to get the raw Unix seconds value. The method name also doesn't clearly reflect what is returned.

## Expected Behavior

- The block lookup method should be renamed to better communicate that it returns a block's number and associated metadata (not a "block pointer").
- The timestamp in the returned data should be a plain unsigned integer representing seconds since the Unix epoch, directly usable without any conversion.
- When a block has no timestamp, the timestamp field should be absent rather than a sentinel value wrapped in a custom type.
- The block number should continue to be returned correctly alongside the timestamp and parent hash.

## Why This Matters

Callers currently need to know about the internal time type and call a specific method on it just to read a Unix timestamp. Returning the raw number of seconds directly makes the API simpler, more consistent, and removes an unnecessary abstraction layer. The rename also improves code clarity by accurately describing what the method actually returns.
