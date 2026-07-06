Update the codebase to migrate from the older Redis client library to the newer, context-aware Redis client library. Ensure all relevant components are updated to accept and use the new client type, and make necessary changes to maintain compatibility and functionality.

*   Update the RateLimiterRedisStore function:
    *   Change the fourth parameter to accept a `*redis.Client` from `github.com/redis/go-redis/v9`.
    *   Remove the sixth parameter, `db int`, so the function now accepts five parameters: `ctx`, `perMin`, `burst`, `redis *redis.Client`, `keyPrefix string`.
    *   Ensure the internal throttled rate-limiting store is compatible with the go-redis/v9 client using `github.com/throttled/throttled/v2` and its context-aware store.

*   Update the NewWithContext function in the coingecko client package:
    *   Change the second parameter to accept a `*redis.Client` from `github.com/redis/go-redis/v9`.
    *   Use the go-redis/v9 API with context support for all internal Redis operations such as GET and SET.

*   Update the NewService function in the ratios service package:
    *   Change the fourth parameter to accept a `*redis.Client` from `github.com/redis/go-redis/v9`.
    *   Update the `Service` struct's `redis` field to hold a `*redis.Client`.

*   Modify the module dependencies:
    *   Remove the dependency on `github.com/gomodule/redigo` from `libs/go.mod` and related `go.sum`.
    *   Add the dependency on `github.com/redis/go-redis/v9`.

*   Update the FilterActiveCreds function in the skus service:
    *   Ensure it treats a credential as active if its `ValidTo` timestamp is greater than or equal to the provided 'now' time.
    *   Implement deterministic behavior by using fixed reference dates in tests to ensure they remain valid over time.

**CRITICAL:** Do not modify any files in the `tests/` directory. The verification system will apply test patches automatically. Your task is to implement the feature in source files only.