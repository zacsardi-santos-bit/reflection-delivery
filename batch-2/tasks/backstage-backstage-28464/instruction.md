Revert the recent library swap in the Backstage backend caching system to use the original Redis adapter. Ensure that the cache manager uses the correct Redis adapter for managing cache connections and update package dependencies accordingly.

*   Update the CacheManager class:
    *   Use the @keyv/redis package for creating Redis cache store connections.
    *   Access the default export via `require('@keyv/redis').default`.
    *   Replace any existing usage of @keyv/valkey in the Redis store factory.

*   Modify the packages/backend-defaults package.json:
    *   Declare @keyv/redis as a dependency.
    *   Remove the existing @keyv/valkey dependency.

*   Ensure correct instantiation behavior:
    *   Instantiate the @keyv/redis adapter constructor exactly once per plugin when cache clients are requested.
    *   For example, requesting cache clients for 3 plugins should result in 3 instances of the @keyv/redis constructor.

*   Configure Keyv instance wrapping:
    *   Disable key prefix management at the Keyv level by setting `useKeyPrefix: false`.
    *   Allow the @keyv/redis store adapter to handle key namespacing to prevent double-prefixing.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.