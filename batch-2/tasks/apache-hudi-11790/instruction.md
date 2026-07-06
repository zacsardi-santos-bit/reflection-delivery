Implement a new lock provider for Hudi that automatically derives the ZooKeeper coordination path from the table's base path and name settings, eliminating the need for explicit ZooKeeper configuration. Ensure both the new and existing lock providers share a common base type and validate their configurations at initialization.

Requirements:

*   Create an abstract class `BaseZookeeperBasedLockProvider` in the `org.apache.hudi.client.transaction.lock` package.
    *   Extend this class from both `ZookeeperBasedLockProvider` and `ZookeeperBasedImplicitBasePathLockProvider`.
    *   Implement the constructor: `BaseZookeeperBasedLockProvider(LockConfiguration lockConfiguration, StorageConfiguration<?> conf)`.
    *   Implement concrete methods: `tryLock(long time, TimeUnit unit)`, `unlock()`, `close()`, and `getLock()`.
    *   Define abstract methods: `getZkBasePath(LockConfiguration lockConfiguration)` and `getLockKey(LockConfiguration lockConfiguration)`.

*   Implement `ZookeeperBasedImplicitBasePathLockProvider`.
    *   Extend `BaseZookeeperBasedLockProvider`.
    *   Automatically derive the ZooKeeper base path from `HoodieCommonConfig.BASE_PATH` and `HoodieTableConfig.HOODIE_TABLE_NAME_KEY`.
    *   Allow initialization and lock acquisition when these properties are present.
    *   Throw `IllegalArgumentException` directly if the table base path or table name is missing during construction.
    *   Accept `null` for `StorageConfiguration` in its constructor.

*   Update `ZookeeperBasedLockProvider`.
    *   Extend `BaseZookeeperBasedLockProvider`.
    *   Require explicit configuration for `ZK_BASE_PATH_PROP_KEY` and `ZK_LOCK_KEY_PROP_KEY`.
    *   Allow initialization and lock acquisition when these properties are present.
    *   Throw `IllegalArgumentException` directly if the ZooKeeper base path or lock key is missing during construction.
    *   Preserve existing behaviors for unlock, reentrant lock exception, and unlock-without-lock when `StorageConfiguration` is `null`.
    *   Accept `null` for `StorageConfiguration` in its constructor.

**CRITICAL:** Do not modify any files in the `tests/` directory.
The verification system will apply test patches automatically.
Your task is to implement the feature in source files only.