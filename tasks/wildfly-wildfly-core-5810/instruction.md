Add a new option to the WildFly Core installation manager operations to explicitly use the default local Maven repository. Ensure compatibility rules are enforced to prevent contradictory configurations.

*   Update `InstMgrConstants`:
    *   Add the constant `USE_DEFAULT_LOCAL_CACHE` with the value "use-default-local-cache".

*   Modify the following classes to handle the new option:
    *   `InstMgrListUpdatesHandler`
    *   `InstMgrPrepareUpdateHandler`
    *   `InstMgrPrepareRevertHandler`
    *   Register `USE_DEFAULT_LOCAL_CACHE` as an optional boolean parameter.
    *   Enforce mutual exclusion validation:
        *   If `use-default-local-cache` is true and `local-cache` is provided, throw an `OperationFailedException` with a message starting with 'WFLYIM0021:'.
        *   If both `use-default-local-cache` and `no-resolve-local-cache` are provided, throw an `OperationFailedException` with a message starting with 'WFLYIM0022:'.
    *   Compute `MavenOptions` based on `use-default-local-cache`:
        *   If true and `local-cache` is not provided, set `getLocalRepository()` to `MavenOptions.LOCAL_MAVEN_REPO`.
        *   If false and `local-cache` is not provided, set `getLocalRepository()` to null.
        *   If false and `local-cache` is provided, set `getLocalRepository()` to the provided path.

*   Update `InstMgrLogger`:
    *   Define method `localCacheWithUseDefaultLocalCache()` annotated with `@Message(id=21)`, returning `OperationFailedException`.
    *   Define method `noResolveLocalCacheWithUseDefaultLocalCache()` annotated with `@Message(id=22)`, returning `OperationFailedException`.

*   Update CLI commands:
    *   For `installer update`:
        *   Accept `--use-default-local-cache` flag.
        *   If used with `--local-cache`, produce an error containing 'WFLYIM0021:'.
    *   For `installer revert`:
        *   Accept `--use-default-local-cache` flag.
        *   If used alone with `--revision`, ensure success and produce a prepared server directory.
        *   If used with `--local-cache`, produce an error containing 'WFLYIM0021:'.

*   Update `AbstractInstMgrUpdateHandler`:
    *   Define a static `AttributeDefinition` field `USE_DEFAULT_LOCAL_CACHE` of type BOOLEAN.
    *   Set alternatives to `LOCAL_CACHE` and `NO_RESOLVE_LOCAL_CACHE`.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.